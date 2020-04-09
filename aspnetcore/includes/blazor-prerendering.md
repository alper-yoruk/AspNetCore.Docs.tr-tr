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
Sunucu Blazor uygulaması önceden işlenirken, tarayıcıyla bağlantı kurulmadığı ndan, JavaScript'e arama gibi belirli eylemler mümkün değildir. Bileşenlerin önceden işlendiğinde farklı işlemesi gerekebilir.

JavaScript interop çağrılarını tarayıcı yla bağlantı kurulana kadar geciktirmek için [OnAfterRenderAsync bileşen yaşam döngüsü olayını](xref:blazor/lifecycle#after-component-render)kullanabilirsiniz. Bu olay yalnızca uygulama tam olarak oluşturulduktan ve istemci bağlantısı kurulduktan sonra çağrılır.

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

Önceki örnek kod için, `setElementText` *wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor `<head>` öğesi içinde bir JavaScript işlevi sağlayın. İşlev ile `IJSRuntime.InvokeVoidAsync` denir ve bir değer döndürmez:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Önceki örnek, Belge Nesnesi Modelini (DOM) yalnızca gösteri amacıyla doğrudan değiştirir. JavaScript'in değişiklik izlemeyi engelleyebilir, Blazorçünkü JavaScript'in doğrudan JavaScript ile DEĞIŞTIRILMESI çoğu senaryoda önerilmez.

Aşağıdaki bileşen, javascript interop'un bir bileşenin başlatma mantığının bir parçası olarak önceden oluşturmayla uyumlu bir şekilde nasıl kullanılacağını gösterir. Bileşen, bir görüntüleme güncelleştirmesini içeriden `OnAfterRenderAsync`tetiklemenin mümkün olduğunu gösterir. Geliştirici bu senaryoda sonsuz bir döngü oluşturmaktan kaçınmalıdır.

Nerede `JSRuntime.InvokeAsync` denir, `ElementRef` bileşen işlendikten sonraya kadar Hiçbir JavaScript öğesi `OnAfterRenderAsync` olduğundan, sadece ve önceki yaşam döngüsü yönteminde kullanılır.

[StateHasChanged,](xref:blazor/lifecycle#state-changes) JavaScript interop çağrısından elde edilen yeni durumla bileşeni yeniden işlemek için çağrılır. Kod sonsuz bir döngü oluşturmaz, çünkü `StateHasChanged` `infoFromJs` yalnızca `null`.

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

Önceki örnek kod için, `setElementText` *wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor `<head>` öğesi içinde bir JavaScript işlevi sağlayın. İşlev ile `IJSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Önceki örnek, Belge Nesnesi Modelini (DOM) yalnızca gösteri amacıyla doğrudan değiştirir. JavaScript'in değişiklik izlemeyi engelleyebilir, Blazorçünkü JavaScript'in doğrudan JavaScript ile DEĞIŞTIRILMESI çoğu senaryoda önerilmez.
