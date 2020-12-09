<span data-ttu-id="aa8d7-101">Blazor sunucu uygulaması prerendering olduğunda, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="aa8d7-102">Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="aa8d7-103">Tarayıcı bağlantısı kurulana kadar JavaScript birlikte çalışma çağrılarını geciktirmek için [Onafterrenderasync bileşen yaşam döngüsü olayını](xref:blazor/components/lifecycle#after-component-render)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="aa8d7-104">Bu olay yalnızca uygulama tam olarak işlendikten ve istemci bağlantısı kurulduktan sonra çağırılır.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="aa8d7-105">Yukarıdaki örnek kod için, `setElementText` `<head>` `wwwroot/index.html` (Blazor webassembly) veya `Pages/_Host.cshtml` (Blazor Server) öğesi içinde bir JavaScript işlevi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="aa8d7-106">İşlevi ile çağrılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ve bir değer döndürmez:</span><span class="sxs-lookup"><span data-stu-id="aa8d7-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="aa8d7-107">Yukarıdaki örnek yalnızca tanıtım amacıyla Belge Nesne Modeli (DOM) değiştirir.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="aa8d7-108">JavaScript, Blazor 'in değişiklik izlemesini kesintiye uğradığı için çoğu senaryoda, JavaScript ile DOM 'ı doğrudan değiştirme önerilmez.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="aa8d7-109">Aşağıdaki bileşen, prerendering ile uyumlu bir şekilde bileşenin başlatma mantığının bir parçası olarak JavaScript birlikte çalışabilirinin nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="aa8d7-110">Bileşen içinden bir işleme güncelleştirmesi tetiklemenin mümkün olduğunu gösterir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="aa8d7-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="aa8d7-111">Geliştirici Bu senaryoda sonsuz bir döngü oluşturmaktan kaçınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="aa8d7-112">Burada <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> çağrılır, `ElementRef` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> bileşen Işlenene kadar JavaScript öğesi olmadığından, yalnızca ' de ' de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="aa8d7-113">JavaScript birlikte çalışma çağrısından alınan yeni durumla birlikte bileşeni yeniden sağlamak için [Statehaschanged](xref:blazor/components/lifecycle#state-changes) çağrılır.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="aa8d7-114">Kod sonsuz döngü oluşturmaz çünkü `StateHasChanged` yalnızca olduğu zaman çağrılır `infoFromJs` `null` .</span><span class="sxs-lookup"><span data-stu-id="aa8d7-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="aa8d7-115">Yukarıdaki örnek kod için, `setElementText` `<head>` `wwwroot/index.html` (Blazor webassembly) veya `Pages/_Host.cshtml` (Blazor Server) öğesi içinde bir JavaScript işlevi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="aa8d7-116">İşlevi ile çağrılır <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> ve bir değer döndürür:</span><span class="sxs-lookup"><span data-stu-id="aa8d7-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="aa8d7-117">Yukarıdaki örnek yalnızca tanıtım amacıyla Belge Nesne Modeli (DOM) değiştirir.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="aa8d7-118">JavaScript, Blazor 'in değişiklik izlemesini kesintiye uğradığı için çoğu senaryoda, JavaScript ile DOM 'ı doğrudan değiştirme önerilmez.</span><span class="sxs-lookup"><span data-stu-id="aa8d7-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
