Blazor sunucu uygulaması prerendering olduğunda, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir. Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.

Tarayıcı bağlantısı kurulana kadar JavaScript birlikte çalışma çağrılarını geciktirmek için [Onafterrenderasync bileşen yaşam döngüsü olayını](xref:blazor/components/lifecycle#after-component-render)kullanabilirsiniz. Bu olay yalnızca uygulama tam olarak işlendikten ve istemci bağlantısı kurulduktan sonra çağırılır.

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

Yukarıdaki örnek kod için, `setElementText` `<head>` `wwwroot/index.html` (Blazor webassembly) veya `Pages/_Host.cshtml` (Blazor Server) öğesi içinde bir JavaScript işlevi sağlayın. İşlevi ile çağrılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ve bir değer döndürmez:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Yukarıdaki örnek yalnızca tanıtım amacıyla Belge Nesne Modeli (DOM) değiştirir. JavaScript, Blazor 'in değişiklik izlemesini kesintiye uğradığı için çoğu senaryoda, JavaScript ile DOM 'ı doğrudan değiştirme önerilmez.

Aşağıdaki bileşen, prerendering ile uyumlu bir şekilde bileşenin başlatma mantığının bir parçası olarak JavaScript birlikte çalışabilirinin nasıl kullanılacağını göstermektedir. Bileşen içinden bir işleme güncelleştirmesi tetiklemenin mümkün olduğunu gösterir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> . Geliştirici Bu senaryoda sonsuz bir döngü oluşturmaktan kaçınmalıdır.

Burada <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> çağrılır, `ElementRef` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> bileşen Işlenene kadar JavaScript öğesi olmadığından, yalnızca ' de ' de kullanılır.

JavaScript birlikte çalışma çağrısından alınan yeni durumla birlikte bileşeni yeniden sağlamak için [Statehaschanged](xref:blazor/components/lifecycle#state-changes) çağrılır. Kod sonsuz döngü oluşturmaz çünkü `StateHasChanged` yalnızca olduğu zaman çağrılır `infoFromJs` `null` .

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

Yukarıdaki örnek kod için, `setElementText` `<head>` `wwwroot/index.html` (Blazor webassembly) veya `Pages/_Host.cshtml` (Blazor Server) öğesi içinde bir JavaScript işlevi sağlayın. İşlevi ile çağrılır <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> ve bir değer döndürür:

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
