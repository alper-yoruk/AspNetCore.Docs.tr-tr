---
title: ASP.NET Core Blazor olay işleme
author: guardrex
description: Olay bağımsız Blazordeğişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: a9b0d0efd4afd4941bd4d93f33adecdf3288992f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767076"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor olay işleme

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razor bileşenleri olay işleme özellikleri sağlar. Temsilci türü belirtilmiş bir değer ile [`@on{EVENT}`](xref:mvc/views/razor#onevent) ADLı bir HTML öğesi `@onclick`özniteliği için (örneğin,), Razor bileşeni öznitelik değerini bir olay işleyicisi olarak değerlendirir.

Aşağıdaki kod, Kullanıcı arabiriminde `UpdateHeading` düğme seçildiğinde yöntemini çağırır:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Aşağıdaki kod, Kullanıcı arabiriminde `CheckChanged` onay kutusu değiştirildiğinde yöntemini çağırır:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task>. [Statehaschanged](xref:blazor/lifecycle#state-changes)el ile çağırmanız gerekmez. Özel durumlar oluştuğunda günlüğe kaydedilir.

Aşağıdaki örnekte, `UpdateHeading` düğme seçildiğinde zaman uyumsuz olarak çağrılır:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a>Olay bağımsız değişken türleri

Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir. Yöntem çağrısında bir olay türü belirtmek yalnızca, olay türü yönteminde kullanılıyorsa gereklidir.

Desteklenir `EventArgs` , aşağıdaki tabloda gösterilmiştir.

| Olay            | Sınıf                | DOM olayları ve notları |
| ---------------- | -------------------- | -------------------- |
| Pano        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Sürükleyin             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`ve `DataTransferItem` öğe verilerini sürüklemiş tutun. |
| Hata            | `ErrorEventArgs`     | `onerror` |
| Olay            | `EventArgs`          | *Genel*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Yapıştırır*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Giriş*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medya*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Çı            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>İçin `relatedTarget`destek içermez. |
| Giriş            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klavye         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Fare            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Fare işaretçisi    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Fare tekerleği      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| İlerleme durumu         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Dokunma            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder. |

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [ASP.NET Core başvuru kaynağındaki EventArgs sınıfları (DotNet/aspnetcore sürümü/3.1 dalı)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN Web belgeleri: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; , hangi HTML öğelerinin her Dom olayını destekledikleri hakkında bilgiler içerir.

## <a name="lambda-expressions"></a>Lambda ifadeleri

[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir. Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni (`MouseEventArgs`) ve düğme numarası (`buttonNumber`) geçiren üç düğme oluşturur:

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Döngü değişkenini (`i`) bir `for` döngüde doğrudan bir lambda ifadesinde kullanmayın. **not** Aksi halde, tüm lambda ifadeleri tarafından değeri tüm Lambdalar aynı olmasına neden olan `i`değişken aynı değişken kullanılır. Her zaman değerini yerel bir değişkende (`buttonNumber` önceki örnekte) yakalayın ve sonra kullanın.

## <a name="eventcallback"></a>EventCallback

İç içe bileşenler içeren yaygın bir senaryo, alt bileşen olayı&mdash;olduğunda bir üst bileşenin yöntemini, örneğin bir `onclick` olay gerçekleştiğinde bir olay oluştuğunda çalıştırmak için gereklidir. Olayları bileşenler genelinde göstermek için bir `EventCallback`kullanın. Bir üst bileşen bir alt bileşene geri çağırma yöntemi atayabilir `EventCallback`.

Örnek `ChildComponent` uygulamada (*Bileşenler/childcomponent. Razor*), bir düğmenin `onclick` işleyicisinin örnekten bir `EventCallback` temsilci alacak şekilde nasıl ayarlandığını gösterir. `ParentComponent` `EventCallback` ,, Bir çevre `MouseEventArgs`cihazından bir `onclick` olay için uygun olan ile öğesine yazılır:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

, `ParentComponent` Alt öğenin `EventCallback<T>` (`OnClickCallback`) metodunu kendi `ShowMessage` yöntemine ayarlar.

*Pages/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Düğme ' de seçildiğinde `ChildComponent`:

* `ParentComponent`Öğesinin `ShowMessage` yöntemi çağrılır. `_messageText`güncelleştirilir ve içinde görüntülenir `ParentComponent`.
* Geri çağırma yönteminde (`ShowMessage`) [statehaschanged](xref:blazor/lifecycle#state-changes) çağrısı gerekli değildir. `StateHasChanged`alt olaylar, alt öğe içinde yürütülen `ParentComponent`olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.

`EventCallback`ve `EventCallback<T>` zaman uyumsuz temsilcilere izin verir. `EventCallback<T>`kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir. `EventCallback`zayıf ve bağımsız değişken türüne izin veriyor.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Bir `EventCallback` veya `EventCallback<T>` ile `InvokeAsync` çağırın ve şunu bekler <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Olay `EventCallback` işleme `EventCallback<T>` ve bağlama bileşeni parametreleri için ve kullanın.

Kesin olarak belirlenmiş `EventCallback<T>` türü tercih `EventCallback`edin. `EventCallback<T>`bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar. Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır. Geri `EventCallback` çağırmaya hiçbir değer geçirilmemişse kullanın.

## <a name="prevent-default-actions"></a>Varsayılan eylemleri engelle

Bir olayın [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) varsayılan eylemini engellemek için Directive özniteliğini kullanın.

Giriş cihazında bir anahtar seçildiğinde ve öğe odağı bir metin kutusunda olduğunda, bir tarayıcı normalde metin kutusunda anahtarın karakterini görüntüler. Aşağıdaki örnekte, `@onkeypress:preventDefault` Directive özniteliği belirtilerek varsayılan davranış engellenir. Sayaç artar ve **+** anahtar `<input>` öğenin değerine yakalanmaz:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

`@on{EVENT}:preventDefault` Özniteliği bir değer olmadan belirtmek ile `@on{EVENT}:preventDefault="true"`eşdeğerdir.

Özniteliğin değeri de bir ifade olabilir. `_shouldPreventDefault` Aşağıdaki örnekte, `bool` ya `true` `false`da olarak ayarlanan bir alandır:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Varsayılan eylemi engellemek için bir olay işleyicisi gerekli değildir. Olay işleyicisi ve varsayılan eylem senaryolarına bağımsız olarak bir şekilde kullanılabilir.

## <a name="stop-event-propagation"></a>Olay yaymayı durdur

Olay yaymayı [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) durdurmak için Directive özniteliğini kullanın.

Aşağıdaki örnekte, onay kutusunun seçilmesi ikinci alt `<div>` öğeden üst `<div>`öğeye yayılan olay tıklamasını önler:

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
