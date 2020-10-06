---
title: ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırın Blazor
author: guardrex
description: Uygulamalarda .NET metotlarından JavaScript işlevlerini çağırmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: d36140067ba6e75f2d00cb86ea488e40d28bd86f
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762171"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="f5679-103">ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırın Blazor</span><span class="sxs-lookup"><span data-stu-id="f5679-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="f5679-104">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f5679-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f5679-105">Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="f5679-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="f5679-106">Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f5679-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="f5679-107">Bu makalede, .NET 'ten JavaScript işlevlerini çağırma ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f5679-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="f5679-108">JavaScript 'ten .NET yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:blazor/call-dotnet-from-javascript> ..</span><span class="sxs-lookup"><span data-stu-id="f5679-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="f5679-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5679-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f5679-110">.NET 'ten JavaScript 'e çağrı yapmak için <xref:Microsoft.JSInterop.IJSRuntime> soyutlamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f5679-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="f5679-111">JS birlikte çalışma çağrıları vermek için, bu <xref:Microsoft.JSInterop.IJSRuntime> soyutlamayı bileşeninizdeki ekler.</span><span class="sxs-lookup"><span data-stu-id="f5679-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="f5679-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> herhangi bir sayıda JSON seri hale getirilebilir bağımsız değişkenle birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır.</span><span class="sxs-lookup"><span data-stu-id="f5679-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="f5679-113">İşlev tanımlayıcısı genel kapsama ( `window` ) göredir.</span><span class="sxs-lookup"><span data-stu-id="f5679-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="f5679-114">Çağırmak isterseniz `window.someScope.someFunction` , tanımlayıcı olur `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="f5679-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="f5679-115">Çağrılmadan önce işlevi kaydetmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f5679-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="f5679-116">Dönüş türünün `T` de JSON seri hale getirilebilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f5679-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="f5679-117">`T` döndürülen JSON türüyle en iyi eşleşen .NET türüyle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="f5679-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="f5679-118">[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) döndüren JavaScript işlevleri ile çağırılır <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f5679-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="f5679-119">`InvokeAsync` Taahhüdünü geri sarar ve Promise tarafından beklenen değeri döndürür.</span><span class="sxs-lookup"><span data-stu-id="f5679-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="f5679-120">Blazor ServerPrerendering etkin olan uygulamalar için, ilk prerendering sırasında JavaScript 'e çağrı yapılamaz.</span><span class="sxs-lookup"><span data-stu-id="f5679-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="f5679-121">JavaScript birlikte çalışma çağrılarının, tarayıcıyla bağlantı kurulana kadar ertelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f5679-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="f5679-122">Daha fazla bilgi için bkz. [bir uygulamanın ne zaman Blazor Server prerendering](#detect-when-a-blazor-server-app-is-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f5679-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="f5679-123">Aşağıdaki örnek [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , JavaScript tabanlı bir kod çözücüsünü temel alır.</span><span class="sxs-lookup"><span data-stu-id="f5679-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="f5679-124">Örnek, geliştirici koddan mevcut bir JavaScript API 'sine bir gereksinimi boşaltan bir C# yönteminden JavaScript işlevinin nasıl çağrılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f5679-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="f5679-125">JavaScript işlevi bir C# yönteminden bir bayt dizisi kabul eder, dizinin kodunu çözer ve görüntülenecek metni bileşene döndürür.</span><span class="sxs-lookup"><span data-stu-id="f5679-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="f5679-126">`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server , geçirilen bir dizinin kodunu çözmek için kullanılan bir JavaScript işlevi sağlayın `TextDecoder` ve kodu çözülen değeri döndürün:</span><span class="sxs-lookup"><span data-stu-id="f5679-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="f5679-127">Önceki örnekte gösterilen kod gibi JavaScript kodu, `.js` betik dosyasına yönelik bir başvuruya sahip bir JavaScript dosyasından () de yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="f5679-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="f5679-128">Aşağıdaki bileşen:</span><span class="sxs-lookup"><span data-stu-id="f5679-128">The following component:</span></span>

* <span data-ttu-id="f5679-129">`convertArray` `JSRuntime` Bir bileşen düğmesi () seçildiğinde JavaScript işlevini çağırır **`Convert Array`** .</span><span class="sxs-lookup"><span data-stu-id="f5679-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="f5679-130">JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dizeye dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="f5679-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="f5679-131">Dize, görüntüleme için bileşene döndürülür.</span><span class="sxs-lookup"><span data-stu-id="f5679-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="f5679-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="f5679-132">IJSRuntime</span></span>

<span data-ttu-id="f5679-133"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="f5679-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="f5679-134"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı Razor bileşene ekleyin ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f5679-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="f5679-135">`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server bir `handleTickerChanged` JavaScript işlevi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f5679-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="f5679-136">İşlevi ile çağrılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ve bir değer döndürmez:</span><span class="sxs-lookup"><span data-stu-id="f5679-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="f5679-137"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı bir sınıfa () Ekle `.cs` :</span><span class="sxs-lookup"><span data-stu-id="f5679-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="f5679-138">`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server bir `handleTickerChanged` JavaScript işlevi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f5679-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="f5679-139">İşlevi ile çağrılır `JSRuntime.InvokeAsync` ve bir değer döndürür:</span><span class="sxs-lookup"><span data-stu-id="f5679-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="f5679-140">[Buildrendertree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma için `[Inject]` özniteliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f5679-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="f5679-141">Bu konuya eşlik eden istemci tarafı örnek uygulamada, Kullanıcı girişi almak ve bir hoş geldiniz iletisi göstermek üzere DOM ile etkileşime geçen uygulama için iki JavaScript işlevi mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="f5679-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="f5679-142">`showPrompt`: Kullanıcı girişini kabul etmek için bir istem üretir (kullanıcının adı) ve arayan adına adı döndürür.</span><span class="sxs-lookup"><span data-stu-id="f5679-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="f5679-143">`displayWelcome`: Çağıran bir DOM nesnesine çağırandan bir hoş geldiniz iletisi atar `id` `welcome` .</span><span class="sxs-lookup"><span data-stu-id="f5679-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="f5679-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="f5679-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="f5679-145">`<script>` `wwwroot/index.html` Dosyadaki ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyadaki () JavaScript dosyasına başvuran etiketi yerleştirin Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f5679-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="f5679-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="f5679-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="f5679-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="f5679-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="f5679-148">`<script>` `<script>` Etiket dinamik olarak güncelleştirilemediğinden bir bileşen dosyasına etiket yerleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="f5679-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="f5679-149">.NET yöntemleri, çağırarak dosyadaki JavaScript işlevleriyle birlikte çalışır `exampleJsInterop.js` <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f5679-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f5679-150"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlama, senaryolara izin vermek için zaman uyumsuzdur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f5679-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="f5679-151">Uygulama bir Blazor WebAssembly uygulama ise ve bir JavaScript işlevini zaman uyumlu olarak çağırmak istiyorsanız, bunun yerine alt türe çevirme yapın <xref:Microsoft.JSInterop.IJSInProcessRuntime> ve çağırın <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> .</span><span class="sxs-lookup"><span data-stu-id="f5679-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="f5679-152">Çoğu JS birlikte çalışma kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olmasını sağlamak için zaman uyumsuz API 'Leri kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="f5679-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f5679-153">Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımını etkinleştirmek için [ Blazor JavaScript yalıtım ve nesne başvuruları](#blazor-javascript-isolation-and-object-references) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f5679-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="f5679-154">Örnek uygulama, JS birlikte çalışabilirliği göstermek için bir bileşeni içerir.</span><span class="sxs-lookup"><span data-stu-id="f5679-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="f5679-155">Bileşen:</span><span class="sxs-lookup"><span data-stu-id="f5679-155">The component:</span></span>

* <span data-ttu-id="f5679-156">Bir JavaScript istemi aracılığıyla Kullanıcı girişini alır.</span><span class="sxs-lookup"><span data-stu-id="f5679-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="f5679-157">İşlemek için bileşene metni döndürür.</span><span class="sxs-lookup"><span data-stu-id="f5679-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="f5679-158">Bir hoş geldiniz iletisini göstermek için DOM ile etkileşime sahip ikinci bir JavaScript işlevini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f5679-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="f5679-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5679-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
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

<span data-ttu-id="f5679-160">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f5679-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="f5679-161">`TriggerJsPrompt`Bileşen düğme seçilerek yürütüldüğünde **`Trigger JavaScript Prompt`** , `showPrompt` dosyada verilen JavaScript işlevi `wwwroot/exampleJsInterop.js` çağırılır.</span><span class="sxs-lookup"><span data-stu-id="f5679-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="f5679-162">`showPrompt`İşlevi, HTML kodlu ve bileşene döndürülen kullanıcı girişini (kullanıcının adı) kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f5679-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="f5679-163">Bileşen, kullanıcının adını yerel bir değişkende depolar `name` .</span><span class="sxs-lookup"><span data-stu-id="f5679-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="f5679-164">İçinde depolanan dize, `name` `displayWelcome` hoş geldiniz iletisini bir başlık etiketine Işleyen bir JavaScript işlevine iletilen bir hoş geldiniz iletisine eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f5679-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="f5679-165">Void JavaScript işlevini çağırın</span><span class="sxs-lookup"><span data-stu-id="f5679-165">Call a void JavaScript function</span></span>

<span data-ttu-id="f5679-166">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) döndüren JavaScript işlevleri ile çağırılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f5679-166">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="f5679-167">Bir uygulamanın ne zaman Blazor Server prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="f5679-167">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="f5679-168">Öğelere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="f5679-168">Capture references to elements</span></span>

<span data-ttu-id="f5679-169">Bazı JS birlikte çalışma senaryoları HTML öğelerine başvurular gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f5679-169">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="f5679-170">Örneğin, bir kullanıcı arabirimi kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya ya da gibi bir öğe üzerinde komut benzeri API 'Ler çağırmanız gerekebilir `focus` `play` .</span><span class="sxs-lookup"><span data-stu-id="f5679-170">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="f5679-171">Aşağıdaki yaklaşımı kullanarak bir bileşen içindeki HTML öğelerine başvuruları yakalayın:</span><span class="sxs-lookup"><span data-stu-id="f5679-171">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="f5679-172">`@ref`HTML öğesine bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5679-172">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="f5679-173"><xref:Microsoft.AspNetCore.Components.ElementReference>Adı özniteliğin değeriyle eşleşen bir tür alanı tanımlayın `@ref` .</span><span class="sxs-lookup"><span data-stu-id="f5679-173">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="f5679-174">Aşağıdaki örnek, öğesine bir başvuru yakalama göstermektedir `username` `<input>` :</span><span class="sxs-lookup"><span data-stu-id="f5679-174">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="f5679-175">Yalnızca ile etkileşimde bulunmayan boş bir öğenin içeriğini bulunmamalıdır için bir öğe başvurusu kullanın Blazor .</span><span class="sxs-lookup"><span data-stu-id="f5679-175">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="f5679-176">Bu senaryo, üçüncü taraf bir API 'nin öğeye içerik sağladığı durumlarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="f5679-176">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="f5679-177">BlazorÖğesiyle etkileşmediği için, Blazor öğe ve Dom gösterimi arasında bir çakışma olabilir.</span><span class="sxs-lookup"><span data-stu-id="f5679-177">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="f5679-178">Aşağıdaki örnekte, *dangerous* `ul` Blazor Bu öğenin liste öğelerini () doldurmak üzere Dom ile etkileşimde bulunduğundan, sıralanmamış listenin () içeriğini zaman içinde () () zaman zaman aşmaktır `<li>` .</span><span class="sxs-lookup"><span data-stu-id="f5679-178">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="f5679-179">JS birlikte çalışma öğesi içeriğini değiştiriyorsa `MyList` ve Blazor öğe için SLA 'lar uygulamaya çalışırsa, DIFFLER Dom ile eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="f5679-179">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="f5679-180">.NET kodu açısından düşünüldüğünde, <xref:Microsoft.AspNetCore.Components.ElementReference> donuk bir tanıtıcıdır.</span><span class="sxs-lookup"><span data-stu-id="f5679-180">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="f5679-181">İle yapabileceğiniz *tek* şey, <xref:Microsoft.AspNetCore.Components.ElementReference> js birlikte çalışma yoluyla JavaScript koduna geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="f5679-181">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="f5679-182">Bunu yaptığınızda, JavaScript tarafı kodu `HTMLElement` normal Dom API 'leri ile kullanılabilecek bir örnek alır.</span><span class="sxs-lookup"><span data-stu-id="f5679-182">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="f5679-183">Örneğin, aşağıdaki kod bir öğe üzerinde odağı ayarlamaya izin veren bir .NET genişletme yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="f5679-183">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="f5679-184">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="f5679-184">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="f5679-185">Değer döndürmeyen bir JavaScript işlevini çağırmak için kullanın <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f5679-185">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f5679-186">Aşağıdaki kod, yakalanmış olan önceki JavaScript işlevini çağırarak Kullanıcı adı girişi üzerinde odağı ayarlar <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="f5679-186">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="f5679-187">Bir genişletme yöntemi kullanmak için, örneği alan bir statik genişletme yöntemi oluşturun <xref:Microsoft.JSInterop.IJSRuntime> :</span><span class="sxs-lookup"><span data-stu-id="f5679-187">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="f5679-188">`Focus`Yöntemi doğrudan nesnesi üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f5679-188">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="f5679-189">Aşağıdaki örnek, `Focus` yöntemin ad alanından kullanılabilir olduğunu varsayar `JsInteropClasses` :</span><span class="sxs-lookup"><span data-stu-id="f5679-189">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="f5679-190">`username`Değişken yalnızca bileşen işlendikten sonra doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f5679-190">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="f5679-191">Doldurulmamış bir <xref:Microsoft.AspNetCore.Components.ElementReference> JavaScript koduna geçirilirse JavaScript kodu bir değeri alır `null` .</span><span class="sxs-lookup"><span data-stu-id="f5679-191">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="f5679-192">Bileşen işlemeyi tamamladıktan sonra öğe başvurularını değiştirmek için (bir öğe üzerinde ilk odağı ayarlamak için) [ `OnAfterRenderAsync` veya `OnAfterRender` bileşen yaşam döngüsü yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f5679-192">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="f5679-193">Genel türlerle çalışırken ve bir değer döndürürken şunu kullanın <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="f5679-193">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="f5679-194">`GenericMethod` , bir türü olan doğrudan nesnesi üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f5679-194">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="f5679-195">Aşağıdaki örnek, `GenericMethod` ad alanından kullanılabilir olduğunu varsayar `JsInteropClasses` :</span><span class="sxs-lookup"><span data-stu-id="f5679-195">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="f5679-196">Bileşenler arasında başvuru öğeleri</span><span class="sxs-lookup"><span data-stu-id="f5679-196">Reference elements across components</span></span>

<span data-ttu-id="f5679-197"><xref:Microsoft.AspNetCore.Components.ElementReference>Yalnızca bir bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> yönteminde (ve bir öğe başvurusu bir) garanti edilir `struct` , bu nedenle bileşenler arasında bir öğe başvurusu geçirilememelidir.</span><span class="sxs-lookup"><span data-stu-id="f5679-197">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="f5679-198">Bir üst bileşen için bir öğe başvurusunu diğer bileşenlere kullanılabilir hale getirmek için, üst bileşen şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="f5679-198">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="f5679-199">Alt bileşenlerin geri çağırmaları kaydetmesine izin ver.</span><span class="sxs-lookup"><span data-stu-id="f5679-199">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="f5679-200">Geçirilen öğe başvurusuyla olay sırasında kayıtlı geri çağırmaları çağırın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> .</span><span class="sxs-lookup"><span data-stu-id="f5679-200">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="f5679-201">Bu yaklaşım dolaylı olarak, alt bileşenlerin üst öğenin öğe başvurusuyla etkileşime geçmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f5679-201">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="f5679-202">Aşağıdaki Blazor WebAssembly örnekte yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f5679-202">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="f5679-203">İçinde `<head>` `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="f5679-203">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="f5679-204">İçinde `<body>` `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="f5679-204">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="f5679-205">`Pages/Index.razor` (üst bileşen):</span><span class="sxs-lookup"><span data-stu-id="f5679-205">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="f5679-206">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="f5679-206">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="f5679-207">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f5679-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="f5679-208">`Shared/SurveyPrompt.razor` (alt bileşen):</span><span class="sxs-lookup"><span data-stu-id="f5679-208">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="f5679-209">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="f5679-209">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="f5679-210">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f5679-210">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="f5679-211">Harden JS birlikte çalışma çağrıları</span><span class="sxs-lookup"><span data-stu-id="f5679-211">Harden JS interop calls</span></span>

<span data-ttu-id="f5679-212">JS birlikte çalışması, ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak değerlendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f5679-212">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="f5679-213">Varsayılan olarak, bir Blazor Server uygulama bir dakika sonra sunucu üzerinde bir kez js birlikte çalışabilirlik çağrısı çağırır.</span><span class="sxs-lookup"><span data-stu-id="f5679-213">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="f5679-214">Bir uygulama daha agresif zaman aşımına uğrayedebiliyorsanız, aşağıdaki yaklaşımlardan birini kullanarak zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f5679-214">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="f5679-215">İçinde genel olarak `Startup.ConfigureServices` , zaman aşımını belirtin:</span><span class="sxs-lookup"><span data-stu-id="f5679-215">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="f5679-216">Bileşen kodunda çağrı başına, tek bir çağrı zaman aşımını belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="f5679-216">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="f5679-217">Kaynak tükenmesi hakkında daha fazla bilgi için bkz <xref:blazor/security/server/threat-mitigation> ..</span><span class="sxs-lookup"><span data-stu-id="f5679-217">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="f5679-218">Döngüsel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="f5679-218">Avoid circular object references</span></span>

<span data-ttu-id="f5679-219">Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:</span><span class="sxs-lookup"><span data-stu-id="f5679-219">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="f5679-220">.NET yöntemi çağrıları.</span><span class="sxs-lookup"><span data-stu-id="f5679-220">.NET method calls.</span></span>
* <span data-ttu-id="f5679-221">Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.</span><span class="sxs-lookup"><span data-stu-id="f5679-221">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="f5679-222">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="f5679-222">For more information, see the following issues:</span></span>

* [<span data-ttu-id="f5679-223">Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="f5679-223">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="f5679-224">Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="f5679-224">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="f5679-225">Blazor JavaScript yalıtımı ve nesne başvuruları</span><span class="sxs-lookup"><span data-stu-id="f5679-225">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="f5679-226">Blazor Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f5679-226">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="f5679-227">JavaScript yalıtımı aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="f5679-227">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="f5679-228">İçeri aktarılan JavaScript artık genel ad alanını pollutes.</span><span class="sxs-lookup"><span data-stu-id="f5679-228">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="f5679-229">İlişkili JavaScript 'in içe aktarılması için bir kitaplık ve bileşenlerin tüketicileri gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f5679-229">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="f5679-230">Örneğin, aşağıdaki JavaScript modülü bir tarayıcı istemi göstermek için bir JavaScript işlevi dışarı aktarır:</span><span class="sxs-lookup"><span data-stu-id="f5679-230">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="f5679-231">Önceki JavaScript modülünü bir .NET kitaplığına statik web varlığı () olarak ekleyin `wwwroot/exampleJsInterop.js` ve ardından hizmeti kullanarak modülü .net koduna aktarın <xref:Microsoft.JSInterop.IJSRuntime> .</span><span class="sxs-lookup"><span data-stu-id="f5679-231">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="f5679-232">Hizmet, `jsRuntime` Aşağıdaki örnek için (gösterilmez) olarak eklenir:</span><span class="sxs-lookup"><span data-stu-id="f5679-232">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="f5679-233">`import`Önceki örnekteki tanımlayıcı, özellikle bir JavaScript modülünü içeri aktarmak için kullanılan özel bir tanıtıcıdır.</span><span class="sxs-lookup"><span data-stu-id="f5679-233">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="f5679-234">Sabit statik Web varlık yolunu kullanarak modülü belirtin: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="f5679-234">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="f5679-235">Yer tutucu, `{LIBRARY NAME}` kitaplık adıdır.</span><span class="sxs-lookup"><span data-stu-id="f5679-235">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="f5679-236">Yer tutucu, `{PATH UNDER WWWROOT}` altında betiğin yoludur `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="f5679-236">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="f5679-237"><xref:Microsoft.JSInterop.IJSRuntime> modülünü `IJSObjectReference` .NET kodundan bir JavaScript nesnesine başvuruyu temsil eden bir olarak içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="f5679-237"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="f5679-238">Modülünden, `IJSObjectReference` içe aktarılmış JavaScript işlevlerini çağırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="f5679-238">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="f5679-239">`IJSInProcessObjectReference` işlevleri zaman uyumlu olarak çağrılabilen bir JavaScript nesnesine olan başvuruyu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f5679-239">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="f5679-240">`IJSUnmarshalledObjectReference` işlevleri .NET verilerinin serileştirilmesi olmadan çağrılabilen bir JavaScript nesnesine yönelik başvuruyu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f5679-240">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="f5679-241">Bu, Blazor WebAssembly performans önemli olduğunda ' de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f5679-241">This can be used in Blazor WebAssembly when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="f5679-242">Kullanıcı arabirimini (DOM öğeleri) işleyen JavaScript kitaplıklarının kullanımı</span><span class="sxs-lookup"><span data-stu-id="f5679-242">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="f5679-243">Bazen, tarayıcı DOM içinde görünür kullanıcı arabirimi öğeleri üreten JavaScript kitaplıklarını kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f5679-243">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="f5679-244">İlk bakışta, bu durum, Blazor Dağıtılmış Sistem, Dom öğelerinin ağacı üzerinde denetime sahip olma ve bazı dış kodlar Dom ağacını bir kez değiştiğinden ve bunların SLA 'ları uygulama mekanizmasını geçersiz kılacağından hatalara açık hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="f5679-244">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="f5679-245">Bu, Blazor belirli bir kısıtlama değildir.</span><span class="sxs-lookup"><span data-stu-id="f5679-245">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="f5679-246">Aynı zorluk, fark tabanlı kullanıcı arabirimi çerçevesiyle oluşur.</span><span class="sxs-lookup"><span data-stu-id="f5679-246">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="f5679-247">Neyse ki, dışarıdan oluşturulan kullanıcı arabirimini Blazor bileşen Kullanıcı arabirimine güvenilir bir şekilde eklemek çok basittir.</span><span class="sxs-lookup"><span data-stu-id="f5679-247">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="f5679-248">Önerilen yöntem, bileşenin kodunun ( `.razor` dosya) boş bir öğe üretmektir.</span><span class="sxs-lookup"><span data-stu-id="f5679-248">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="f5679-249">BlazorYayılma sisteminin en çok ilgisi olduğu gibi, öğe her zaman boştur, bu nedenle işleyici öğesi için bir değer değil, onun içeriğini tek başına bırakır.</span><span class="sxs-lookup"><span data-stu-id="f5679-249">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="f5679-250">Bu, öğesini rastgele dışarıdan yönetilen içerikle doldurmayı güvenli hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f5679-250">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="f5679-251">Aşağıdaki örnek kavramı gösterir.</span><span class="sxs-lookup"><span data-stu-id="f5679-251">The following example demonstrates the concept.</span></span> <span data-ttu-id="f5679-252">İfadesinin içinde `if` olduğunda `firstRender` `true` , ile bir işlem yapın `myElement` .</span><span class="sxs-lookup"><span data-stu-id="f5679-252">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="f5679-253">Örneğin, doldurmak için bir dış JavaScript kitaplığı çağırın.</span><span class="sxs-lookup"><span data-stu-id="f5679-253">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="f5679-254">Blazor Bu bileşen kaldırılana kadar öğenin içeriğini tek başına bırakır.</span><span class="sxs-lookup"><span data-stu-id="f5679-254">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="f5679-255">Bileşen kaldırıldığında, bileşenin tüm DOM alt ağacı da kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="f5679-255">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

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

<span data-ttu-id="f5679-256">Daha ayrıntılı bir örnek olarak, [Açık kaynaklı Mapbox API 'lerini](https://www.mapbox.com/)kullanarak etkileşimli bir eşleme oluşturan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f5679-256">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="f5679-257">Kendisine yerleştirilmesi gereken ilgili JavaScript modülü `wwwroot/mapComponent.js` aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="f5679-257">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="f5679-258">Yukarıdaki örnekte, dizeyi, `{ACCESS TOKEN}` alacağınız geçerli bir erişim belirteciyle değiştirin https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="f5679-258">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="f5679-259">Doğru stil oluşturmak için, ana bilgisayar HTML sayfasına aşağıdaki stil sayfası etiketini ekleyin ( `index.html` veya `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="f5679-259">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="f5679-260">Yukarıdaki örnek, kullanıcının şu şekilde bir etkileşimli harita Kullanıcı arabirimi üretir:</span><span class="sxs-lookup"><span data-stu-id="f5679-260">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="f5679-261">, Kaydırmak veya yakınlaştırmak için sürükleyebilir.</span><span class="sxs-lookup"><span data-stu-id="f5679-261">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="f5679-262">Önceden tanımlanmış konumlara geçmek için düğmeler ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f5679-262">Click buttons to jump to predefined locations.</span></span>

![Esistol, Birleşik Krallık ve Tokyo, Japonya ' ı seçmek için bkz. Tokyo 'daki mapbox açık Haritası](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="f5679-264">Anlaşılması için önemli noktaları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f5679-264">The key points to understand are:</span></span>

 * <span data-ttu-id="f5679-265">, `<div>` İle ilişkili olduğu kadar `@ref="mapElement"` boş bırakılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="f5679-265">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="f5679-266">Bu nedenle `mapbox-gl.js` , bunu doldurmak ve içeriğini zaman içinde değiştirmek güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="f5679-266">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="f5679-267">Bu tekniği, Kullanıcı arabirimini işleyen herhangi bir JavaScript kitaplığıyla kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f5679-267">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="f5679-268">Ayrıca, bileşen içindeki bir üçüncü taraf JavaScript SPA çerçevesinin Blazor , sayfanın diğer bölümlerine ulaşmaya ve bunları değiştirmeye çalıştıklarında bile bileşenleri ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f5679-268">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="f5679-269">Dış JavaScript kodu için boş olarak söz konusu olmayan öğeleri değiştirme güvenli *değildir* Blazor .</span><span class="sxs-lookup"><span data-stu-id="f5679-269">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="f5679-270">Bu yaklaşımı kullanırken, Blazor Dom öğelerinin nasıl koruduğunu veya yok olduğunu gösteren kuralları göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="f5679-270">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="f5679-271">Önceki örnekte, bileşen düğme tıklama olayları ' nı güvenli bir şekilde işler ve DOM öğeleri varsayılan olarak mümkün olduğunda korunduğu için mevcut eşleme örneğini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="f5679-271">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="f5679-272">Bir döngünün içindeki harita öğelerinin bir listesini işlerinizden `@foreach` , `@key` bileşen örneklerinin korunmasını sağlamak için kullanmak istersiniz.</span><span class="sxs-lookup"><span data-stu-id="f5679-272">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="f5679-273">Aksi takdirde, liste verilerinde yapılan değişiklikler bileşen örneklerinin önceki örneklerin durumunu istenmeyen bir şekilde tutmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f5679-273">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="f5679-274">Daha fazla bilgi için bkz [. @key öğeleri ve bileşenleri korumak için kullanma](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="f5679-274">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="f5679-275">Ayrıca, önceki örnekte, JavaScript mantığını ve bağımlılıklarını bir ES6 modülü içinde kapsüllemek ve tanımlayıcıyı kullanarak dinamik olarak yüklemek nasıl mümkün olduğunu gösterir `import` .</span><span class="sxs-lookup"><span data-stu-id="f5679-275">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="f5679-276">Daha fazla bilgi için bkz. [JavaScript yalıtım ve nesne başvuruları](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="f5679-276">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f5679-277">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f5679-277">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="f5679-278">InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)</span><span class="sxs-lookup"><span data-stu-id="f5679-278">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="f5679-279">Uygulamalarda büyük veri aktarımları gerçekleştirme Blazor Server</span><span class="sxs-lookup"><span data-stu-id="f5679-279">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
