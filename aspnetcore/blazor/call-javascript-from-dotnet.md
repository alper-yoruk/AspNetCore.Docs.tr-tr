---
title: ASP.NET Core'daki .NET yöntemlerinden JavaScript işlevlerini arayınBlazor
author: guardrex
description: Uygulamalardaki Blazor .NET yöntemlerinden JavaScript işlevlerini nasıl çağırılamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977021"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="44858-103">ASP.NET Core'daki .NET yöntemlerinden JavaScript işlevlerini arayınBlazor</span><span class="sxs-lookup"><span data-stu-id="44858-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="44858-104">Javier [Calvarro Nelson,](https://github.com/javiercn) [Daniel Roth](https://github.com/danroth27)ve Luke [Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="44858-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="44858-105">Bir Blazor uygulama JavaScript işlevlerinden .NET yöntemlerinden ve .NET yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="44858-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="44858-106">Bu senaryolar *JavaScript birlikte çalışabilirlik* *(JS interop)* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="44858-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="44858-107">Bu makale, .NET'ten JavaScript işlevlerini çağırarak kapsar.</span><span class="sxs-lookup"><span data-stu-id="44858-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="44858-108">JavaScript'ten .NET yöntemlerini nasıl arayacağıhakkında bilgi için bkz. <xref:blazor/call-dotnet-from-javascript></span><span class="sxs-lookup"><span data-stu-id="44858-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="44858-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44858-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="44858-110">.NET'ten JavaScript'e çağrı `IJSRuntime` yapmak için soyutlamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="44858-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="44858-111">JS interop çağrıları vermek `IJSRuntime` için, bileşeninize soyutlama enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="44858-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="44858-112">Yöntem, `InvokeAsync<T>` herhangi bir sayıda JSON-serializable bağımsız değişkeni ile birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır.</span><span class="sxs-lookup"><span data-stu-id="44858-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="44858-113">İşlev tanımlayıcısı genel kapsama göredir (`window`).</span><span class="sxs-lookup"><span data-stu-id="44858-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="44858-114">Aramak `window.someScope.someFunction`isterseniz, tanımlayıcı `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="44858-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="44858-115">Çağrılmadan önce işlevi kaydetmenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="44858-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="44858-116">İade türü `T` de JSON serileştirilebilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="44858-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="44858-117">`T`döndürülen JSON türüyle en iyi eşlemleri içeren .NET türüyle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="44858-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="44858-118">Ön Blazor oluşturma etkinleştirilmiş Sunucu uygulamaları için, ilk ön oluşturma sırasında JavaScript'e çağrı yapmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="44858-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="44858-119">JavaScript interop çağrıları tarayıcı ile bağlantı kurulana kadar ertelenmelidir.</span><span class="sxs-lookup"><span data-stu-id="44858-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="44858-120">Daha fazla bilgi için, [sunucu Blazor uygulaması nın ön oluşturma](#detect-when-a-blazor-server-app-is-prerendering) bölümü ne zaman algıla'ya bakın.</span><span class="sxs-lookup"><span data-stu-id="44858-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="44858-121">Aşağıdaki örnek, JavaScript tabanlı kod çözücü [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="44858-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="44858-122">Örnek, C# yönteminden javascript işlevinin nasıl çağrıldığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="44858-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="44858-123">JavaScript işlevi C# yönteminden bir bayt dizisini kabul eder, diziyi çözer ve metni görüntülenmek üzere bileşene döndürür.</span><span class="sxs-lookup"><span data-stu-id="44858-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="44858-124">*wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor `TextDecoder` `<head>` öğesi içinde, geçirilen bir dizide şifreyi çözmek ve çözülmüş değeri döndürmek için kullanan bir JavaScript işlevi sağlayın:</span><span class="sxs-lookup"><span data-stu-id="44858-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="44858-125">Önceki örnekte gösterilen kod gibi JavaScript kodu, komut dosyası dosyasına atıfta bulunularak javascript dosyasından *(.js)* de yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="44858-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="44858-126">Aşağıdaki bileşen:</span><span class="sxs-lookup"><span data-stu-id="44858-126">The following component:</span></span>

* <span data-ttu-id="44858-127">`convertArray` Bir bileşen düğmesi **(Diziyi Dönüştür)** seçildiğinde JavaScript işlevini kullanır. `JSRuntime`</span><span class="sxs-lookup"><span data-stu-id="44858-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="44858-128">JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dize dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="44858-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="44858-129">Dize görüntülenmek üzere bileşene döndürülür.</span><span class="sxs-lookup"><span data-stu-id="44858-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="44858-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="44858-130">IJSRuntime</span></span>

<span data-ttu-id="44858-131">Soyutlamayı `IJSRuntime` kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="44858-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="44858-132">Soyutlamayı `IJSRuntime` Jilet bileşenine enjekte edin (*.razor):*</span><span class="sxs-lookup"><span data-stu-id="44858-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="44858-133">*wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor öğesi `handleTickerChanged` içinde `<head>` bir JavaScript işlevi sağlar.</span><span class="sxs-lookup"><span data-stu-id="44858-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="44858-134">İşlev ile `IJSRuntime.InvokeVoidAsync` denir ve bir değer döndürmez:</span><span class="sxs-lookup"><span data-stu-id="44858-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="44858-135">Soyutlamayı `IJSRuntime` bir sınıfa enjekte edin (*.cs):*</span><span class="sxs-lookup"><span data-stu-id="44858-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="44858-136">*wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor öğesi `handleTickerChanged` içinde `<head>` bir JavaScript işlevi sağlar.</span><span class="sxs-lookup"><span data-stu-id="44858-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="44858-137">İşlev ile `JSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:</span><span class="sxs-lookup"><span data-stu-id="44858-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="44858-138">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma `[Inject]` için özniteliği kullanın:</span><span class="sxs-lookup"><span data-stu-id="44858-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="44858-139">Bu konuya eşlik eden istemci tarafındaki örnek uygulamada, kullanıcı girişi almak ve hoş geldiniz iletisi görüntülemek için DOM ile etkileşime geçen uygulama için iki JavaScript işlevi kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="44858-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="44858-140">`showPrompt`&ndash; Kullanıcı girişi (kullanıcının adı) kabul etmek için bir istem üretir ve adı arayana döndürür.</span><span class="sxs-lookup"><span data-stu-id="44858-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="44858-141">`displayWelcome`&ndash; Arayankişiden bir DOM nesnesine hoş `id` geldiniz `welcome`iletisi atar.</span><span class="sxs-lookup"><span data-stu-id="44858-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="44858-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="44858-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="44858-143">JavaScript `<script>` dosyasına başvuran etiketi *wwwroot/index.html* dosyasınaBlazor (WebAssembly) veya *Pages/_Host.cshtml* dosyasına (Sunucu)Blazor yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="44858-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="44858-144">*wwwroot/index.html* Blazor (WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="44858-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="44858-145">*Sayfa/_Host.cshtml* Blazor (Sunucu):</span><span class="sxs-lookup"><span data-stu-id="44858-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="44858-146">Etiket dinamik olarak `<script>` güncelleştirilemediği için `<script>` bileşen dosyasına etiket yerleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="44858-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="44858-147">.NET yöntemleri javascript fonksiyonları ile interop *örnekjsInterop.js* dosyaarayarak `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="44858-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="44858-148">Soyutlama, `IJSRuntime` Sunucu senaryolarına izin vermek Blazor için bir senkrondur.</span><span class="sxs-lookup"><span data-stu-id="44858-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="44858-149">Uygulama bir Blazor WebAssembly uygulamasıysa ve bir JavaScript işlevini eşzamanlı olarak çağırmak `IJSInProcessRuntime` istiyorsanız, bunun yerine aşağı döküm ve çağrı. `Invoke<T>`</span><span class="sxs-lookup"><span data-stu-id="44858-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="44858-150">Çoğu JS interop kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olduğundan emin olmak için async API'lerini kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="44858-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="44858-151">Örnek uygulama JS interop göstermek için bir bileşen içerir.</span><span class="sxs-lookup"><span data-stu-id="44858-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="44858-152">Bileşen:</span><span class="sxs-lookup"><span data-stu-id="44858-152">The component:</span></span>

* <span data-ttu-id="44858-153">JavaScript komut istemi ile kullanıcı girişi alır.</span><span class="sxs-lookup"><span data-stu-id="44858-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="44858-154">Metni işleme için bileşene döndürür.</span><span class="sxs-lookup"><span data-stu-id="44858-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="44858-155">Hoş geldiniz iletisi görüntülemek için DOM ile etkileşimedebilen ikinci bir JavaScript işlevini çağırır.</span><span class="sxs-lookup"><span data-stu-id="44858-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="44858-156">*Sayfa/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="44858-156">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="44858-157">Bileşenin **Trigger JavaScript Komut İstedüğmesi** seçilerek yürütüldüğünde, `showPrompt` `TriggerJsPrompt` *wwwroot/exampleJsInterop.js* dosyasında sağlanan JavaScript işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="44858-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="44858-158">İşlev, `showPrompt` HTML kodlu kullanıcı girişi (kullanıcı adı) kabul eder ve bileşene döndürülür.</span><span class="sxs-lookup"><span data-stu-id="44858-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="44858-159">Bileşen, kullanıcının adını yerel bir değişkende `name`depolar.</span><span class="sxs-lookup"><span data-stu-id="44858-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="44858-160">Depolanan `name` dize, hoş geldiniz iletisini bir başlık etiketine işleyen `displayWelcome`bir JavaScript işlevine geçirilen bir karşılama iletisine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="44858-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="44858-161">Geçersiz bir JavaScript işlevini arayın</span><span class="sxs-lookup"><span data-stu-id="44858-161">Call a void JavaScript function</span></span>

<span data-ttu-id="44858-162">[Void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [tanımlanmamış](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) döndüren `IJSRuntime.InvokeVoidAsync`JavaScript işlevleri .</span><span class="sxs-lookup"><span data-stu-id="44858-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="44858-163">Bir Blazor Sunucu uygulamasının ne zaman önceden oluşturulaca-sonuç veriyi algıla</span><span class="sxs-lookup"><span data-stu-id="44858-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="44858-164">Öğelere yapılan başvuruları yakalama</span><span class="sxs-lookup"><span data-stu-id="44858-164">Capture references to elements</span></span>

<span data-ttu-id="44858-165">Bazı JS interop senaryoları HTML öğelerine başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="44858-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="44858-166">Örneğin, bir UI kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya komut benzeri API'leri bir öğe üzerinde aramanız `focus` gerekebilir. `play`</span><span class="sxs-lookup"><span data-stu-id="44858-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="44858-167">Aşağıdaki yaklaşımı kullanarak bir bileşendeki HTML öğelerine yapılan başvuruları yakalayın:</span><span class="sxs-lookup"><span data-stu-id="44858-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="44858-168">HTML `@ref` öğesine bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="44858-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="44858-169">Adı öznitelik `ElementReference` değeriyle eşleşen bir `@ref` tür alanı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="44858-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="44858-170">Aşağıdaki örnek, `username` `<input>` öğeye bir başvuru yakalama gösterir:</span><span class="sxs-lookup"><span data-stu-id="44858-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="44858-171">Yalnızca etkileşime girmeyen boş bir öğenin içeriğini mutasyona Blazoruğratmak için bir öğe başvurusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="44858-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="44858-172">Bu senaryo, üçüncü taraf api öğesine içerik sağladığında yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="44858-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="44858-173">Öğeile Blazor etkileşime girmediği için, 'öğenin temsili Blazorile DOM arasında bir çakışma olasılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="44858-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="44858-174">Aşağıdaki örnekte, bu öğenin liste öğeleridoldurmak için DOM ile`ul` Blazor etkileşimde çünkü sıralanmamış listenin içeriğini mutasyona`<li>`uğratmak için *tehlikelidir* ( ):</span><span class="sxs-lookup"><span data-stu-id="44858-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="44858-175">JS interop öğenin `MyList` içeriğini mutasyona Blazor uğrar ve elemandiffs uygulamak için çalışırsa, diffs DOM eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="44858-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="44858-176">.NET kodu söz konusu olduğunda, opak `ElementReference` bir tutamaçtır.</span><span class="sxs-lookup"><span data-stu-id="44858-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="44858-177">Yapabileceğiniz *tek* şey JS interop üzerinden JavaScript koduna `ElementReference` geçmektir.</span><span class="sxs-lookup"><span data-stu-id="44858-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="44858-178">Bunu yaptığınızda, JavaScript yan kodu `HTMLElement` normal DOM API'leri ile kullanabileceği bir örnek alır.</span><span class="sxs-lookup"><span data-stu-id="44858-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="44858-179">Örneğin, aşağıdaki kod, bir öğenin odağının ayarlanmasını sağlayan bir .NET uzantı yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="44858-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="44858-180">*örnekJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="44858-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="44858-181">Değer vermeyen bir JavaScript işlevini çağırmak için `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="44858-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="44858-182">Aşağıdaki kod yakalanan `ElementReference`önceki JavaScript işlevi arayarak kullanıcı adı girişi üzerinde odak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="44858-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="44858-183">Uzantı yöntemini kullanmak `IJSRuntime` için, örneği alan statik bir uzantı yöntemi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="44858-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="44858-184">Yöntem `Focus` doğrudan nesne üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="44858-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="44858-185">Aşağıdaki örnek, yöntemin `Focus` `JsInteropClasses` ad alanından kullanılabildiğini varsayar:</span><span class="sxs-lookup"><span data-stu-id="44858-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="44858-186">Değişken `username` yalnızca bileşen işlendikten sonra doldurulur.</span><span class="sxs-lookup"><span data-stu-id="44858-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="44858-187">Doldurulmamış bir `ElementReference` JavaScript koduna aktarılırsa, JavaScript kodu `null`.</span><span class="sxs-lookup"><span data-stu-id="44858-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="44858-188">Bileşen işleme bittikten sonra eleman başvurularını işlemek için (bir öğeye ilk odağı ayarlamak için) [OnAfterRenderAsync veya OnAfterRender bileşen yaşam döngüsü yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="44858-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="44858-189">Genel türlerle çalışırken ve bir değer döndürerken, [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1)kullanın:</span><span class="sxs-lookup"><span data-stu-id="44858-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="44858-190">`GenericMethod`türü olan nesneye doğrudan çağrılır.</span><span class="sxs-lookup"><span data-stu-id="44858-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="44858-191">Aşağıdaki örnek, `GenericMethod` `JsInteropClasses` ad alanından kullanılabilir olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="44858-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="44858-192">Bileşenler arasında referans öğeleri</span><span class="sxs-lookup"><span data-stu-id="44858-192">Reference elements across components</span></span>

<span data-ttu-id="44858-193">Bir `ElementReference` bileşenin `OnAfterRender` yönteminde geçerli olduğu garanti edilir (ve `struct`bir öğe başvurusu birdir), bu nedenle bir öğe başvurusu bileşenler arasında geçirilemez.</span><span class="sxs-lookup"><span data-stu-id="44858-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="44858-194">Bir üst bileşenin bir öğe referansını diğer bileşenler için kullanılabilir hale getirebilmek için, üst bileşen şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="44858-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="44858-195">Alt bileşenlerin geri aramaları kaydetmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="44858-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="44858-196">Geçen öğe başvurusuyla `OnAfterRender` olay sırasında kayıtlı geri aramaları çağırın.</span><span class="sxs-lookup"><span data-stu-id="44858-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="44858-197">Dolaylı olarak, bu yaklaşım alt bileşenlerin üst öğe başvurusu ile etkileşim sağlar.</span><span class="sxs-lookup"><span data-stu-id="44858-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="44858-198">Aşağıdaki Blazor WebAssembly örneği yaklaşımı göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="44858-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="44858-199">`<head>` *wwwroot/index.html olarak:*</span><span class="sxs-lookup"><span data-stu-id="44858-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="44858-200">`<body>` *wwwroot/index.html olarak:*</span><span class="sxs-lookup"><span data-stu-id="44858-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="44858-201">*Sayfalar/Index.razor* (üst bileşen):</span><span class="sxs-lookup"><span data-stu-id="44858-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="44858-202">*Sayfa/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="44858-202">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="44858-203">*Paylaşılan/SurveyPrompt.razor* (alt bileşeni):</span><span class="sxs-lookup"><span data-stu-id="44858-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="44858-204">*Paylaşılan/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="44858-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="44858-205">Harden JS interop aramaları</span><span class="sxs-lookup"><span data-stu-id="44858-205">Harden JS interop calls</span></span>

<span data-ttu-id="44858-206">JS interop ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak kabul edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="44858-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="44858-207">Varsayılan olarak, Blazor bir Sunucu uygulaması bir dakika sonra sunucudaki JS interop çağrılarını kez times.</span><span class="sxs-lookup"><span data-stu-id="44858-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="44858-208">Bir uygulama 10 saniye gibi daha agresif bir zaman adabına tahammül edebilirse, zaman anına aşağıdaki yaklaşımlardan birini kullanarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="44858-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="44858-209">Genel `Startup.ConfigureServices`olarak, zaman anına belirtin:</span><span class="sxs-lookup"><span data-stu-id="44858-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="44858-210">Bileşen kodunda çağırma başına, tek bir arama zaman ayarı belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="44858-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="44858-211">Kaynak tükenmesi hakkında daha <xref:security/blazor/server>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="44858-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="44858-212">Dairesel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="44858-212">Avoid circular object references</span></span>

<span data-ttu-id="44858-213">Dairesel başvurular içeren nesneler istemcide aşağıdakiler için serileştirilemez:</span><span class="sxs-lookup"><span data-stu-id="44858-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="44858-214">.NET yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="44858-214">.NET method calls.</span></span>
* <span data-ttu-id="44858-215">İade türü dairesel referansları olduğunda JavaScript yöntemi C#'dan çağrı verir.</span><span class="sxs-lookup"><span data-stu-id="44858-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="44858-216">Daha fazla bilgi için aşağıdaki sorunlara bakın:</span><span class="sxs-lookup"><span data-stu-id="44858-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="44858-217">Dairesel başvurular desteklenmez, iki tane alın (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="44858-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="44858-218">Öneri: Serileştirme yaparken dairesel başvuruları işlemek için mekanizma ekleme (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="44858-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="44858-219">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="44858-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="44858-220">InteropComponent.razor örnek (dotnet/AspNetCore GitHub deposu, 3.1 sürüm şubesi)</span><span class="sxs-lookup"><span data-stu-id="44858-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="44858-221">[Sunucu uygulamalarında Blazor büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="44858-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
