---
---
Blazor sunucu uygulaması prerendering olduğunda, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir. Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.

Tarayıcı bağlantısı kurulana kadar JavaScript birlikte çalışma çağrılarını geciktirmek için [Onafterrenderasync bileşen yaşam döngüsü olayını](xref:blazor/lifecycle#after-component-render)kullanabilirsiniz. Bu olay yalnızca uygulama tam olarak işlendikten ve istemci bağlantısı kurulduktan sonra çağırılır.

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

Yukarıdaki örnek kod için, *Wwwroot/index.html* (Blazor `setElementText` Webassembly) `<head>` veya *Pages/_Host. cshtml* (Blazor Server) öğesi içinde bir JavaScript işlevi sağlayın. İşlevi ile `IJSRuntime.InvokeVoidAsync` çağrılır ve bir değer döndürmez:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Yukarıdaki örnek yalnızca tanıtım amacıyla Belge Nesne Modeli (DOM) değiştirir. JavaScript, Blazor 'in değişiklik izlemesini kesintiye uğradığı için çoğu senaryoda, JavaScript ile DOM 'ı doğrudan değiştirme önerilmez.

Aşağıdaki bileşen, prerendering ile uyumlu bir şekilde bileşenin başlatma mantığının bir parçası olarak JavaScript birlikte çalışabilirinin nasıl kullanılacağını göstermektedir. Bileşen içinden bir işleme güncelleştirmesi tetiklemenin mümkün olduğunu gösterir `OnAfterRenderAsync`. Geliştirici Bu senaryoda sonsuz bir döngü oluşturmaktan kaçınmalıdır.

Burada `JSRuntime.InvokeAsync` çağrılır, bileşen `ElementRef` işlenene kadar JavaScript `OnAfterRenderAsync` öğesi olmadığından, yalnızca ' de ' de kullanılır.

JavaScript birlikte çalışma çağrısından alınan yeni durumla birlikte bileşeni yeniden sağlamak için [Statehaschanged](xref:blazor/lifecycle#state-changes) çağrılır. Kod sonsuz döngü oluşturmaz çünkü `StateHasChanged` yalnızca olduğu `infoFromJs` `null`zaman çağrılır.

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

Yukarıdaki örnek kod için, *Wwwroot/index.html* (Blazor `setElementText` Webassembly) `<head>` veya *Pages/_Host. cshtml* (Blazor Server) öğesi içinde bir JavaScript işlevi sağlayın. İşlevi ile `IJSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Yukarıdaki örnek yalnızca tanıtım amacıyla Belge Nesne Modeli (DOM) değiştirir. JavaScript, Blazor 'in değişiklik izlemesini kesintiye uğradığı için çoğu senaryoda, JavaScript ile DOM 'ı doğrudan değiştirme önerilmez.
