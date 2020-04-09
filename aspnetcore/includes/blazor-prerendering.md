---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659721"
---
<span data-ttu-id="04b2b-101">Sunucu Blazor uygulaması önceden işlenirken, tarayıcıyla bağlantı kurulmadığı ndan, JavaScript'e arama gibi belirli eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="04b2b-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="04b2b-102">Bileşenlerin önceden işlendiğinde farklı işlemesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="04b2b-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="04b2b-103">JavaScript interop çağrılarını tarayıcı yla bağlantı kurulana kadar geciktirmek için [OnAfterRenderAsync bileşen yaşam döngüsü olayını](xref:blazor/lifecycle#after-component-render)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="04b2b-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="04b2b-104">Bu olay yalnızca uygulama tam olarak oluşturulduktan ve istemci bağlantısı kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="04b2b-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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

<span data-ttu-id="04b2b-105">Önceki örnek kod için, `setElementText` *wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor `<head>` öğesi içinde bir JavaScript işlevi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="04b2b-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="04b2b-106">İşlev ile `IJSRuntime.InvokeVoidAsync` denir ve bir değer döndürmez:</span><span class="sxs-lookup"><span data-stu-id="04b2b-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="04b2b-107">Önceki örnek, Belge Nesnesi Modelini (DOM) yalnızca gösteri amacıyla doğrudan değiştirir.</span><span class="sxs-lookup"><span data-stu-id="04b2b-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="04b2b-108">JavaScript'in değişiklik izlemeyi engelleyebilir, Blazorçünkü JavaScript'in doğrudan JavaScript ile DEĞIŞTIRILMESI çoğu senaryoda önerilmez.</span><span class="sxs-lookup"><span data-stu-id="04b2b-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="04b2b-109">Aşağıdaki bileşen, javascript interop'un bir bileşenin başlatma mantığının bir parçası olarak önceden oluşturmayla uyumlu bir şekilde nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="04b2b-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="04b2b-110">Bileşen, bir görüntüleme güncelleştirmesini içeriden `OnAfterRenderAsync`tetiklemenin mümkün olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="04b2b-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="04b2b-111">Geliştirici bu senaryoda sonsuz bir döngü oluşturmaktan kaçınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="04b2b-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="04b2b-112">Nerede `JSRuntime.InvokeAsync` denir, `ElementRef` bileşen işlendikten sonraya kadar Hiçbir JavaScript öğesi `OnAfterRenderAsync` olduğundan, sadece ve önceki yaşam döngüsü yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04b2b-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="04b2b-113">[StateHasChanged,](xref:blazor/lifecycle#state-changes) JavaScript interop çağrısından elde edilen yeni durumla bileşeni yeniden işlemek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="04b2b-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="04b2b-114">Kod sonsuz bir döngü oluşturmaz, çünkü `StateHasChanged` `infoFromJs` yalnızca `null`.</span><span class="sxs-lookup"><span data-stu-id="04b2b-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

<span data-ttu-id="04b2b-115">Önceki örnek kod için, `setElementText` *wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor `<head>` öğesi içinde bir JavaScript işlevi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="04b2b-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="04b2b-116">İşlev ile `IJSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:</span><span class="sxs-lookup"><span data-stu-id="04b2b-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="04b2b-117">Önceki örnek, Belge Nesnesi Modelini (DOM) yalnızca gösteri amacıyla doğrudan değiştirir.</span><span class="sxs-lookup"><span data-stu-id="04b2b-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="04b2b-118">JavaScript'in değişiklik izlemeyi engelleyebilir, Blazorçünkü JavaScript'in doğrudan JavaScript ile DEĞIŞTIRILMESI çoğu senaryoda önerilmez.</span><span class="sxs-lookup"><span data-stu-id="04b2b-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
