---
title: ASP.NET Blazor Core olay işleme
author: guardrex
description: Olay Blazorbağımsız değişken türleri, olay geri aramaları ve varsayılan tarayıcı olaylarını yönetme gibi 'olay işleme özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511372"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor olay işleme

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

Jilet bileşenleri olay işleme özellikleri sağlar. Temsilci türünde bir [`@on{EVENT}`](xref:mvc/views/razor#onevent) değere sahip `@onclick`(örneğin), adlandırılmış bir HTML öğesi için, Bir Jilet bileşeni özniteliğin değerini olay işleyicisi olarak değerlendirir.

Aşağıdaki kod, `UpdateHeading` ui'de düğme seçildiğinde yöntemi çağırır:

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

Aşağıdaki kod, `CheckChanged` arama-bilgili kutu değiştirildiğinde yöntemi çağırır:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Olay işleyicileri de asynchronous olabilir <xref:System.Threading.Tasks.Task>ve bir . [StateHasChanged'i](xref:blazor/lifecycle#state-changes)el ile aramanız gerekmez. Özel durumlar oluştuğunda günlüğe kaydedilir.

Aşağıdaki örnekte, `UpdateHeading` düğme seçildiğinde eş senkronize olarak adlandırılır:

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

Bazı olaylar için olay bağımsız değişken türlerine izin verilir. Yöntem çağrısında bir olay türü belirtilmesi yalnızca yöntemde olay türü kullanılıyorsa gereklidir.

Desteklenenler `EventArgs` aşağıdaki tabloda gösterilir.

| Olay            | Sınıf                | DOM etkinlikleri ve notları |
| ---------------- | -------------------- | -------------------- |
| Pano        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Sürükleyin             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`ve `DataTransferItem` sürüklenen öğe verilerini tutun. |
| Hata            | `ErrorEventArgs`     | `onerror` |
| Olay            | `EventArgs`          | *Genel*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Pano*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Girdi*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medya*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Odak            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Destek `relatedTarget`içermez. |
| Girdi            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klavye         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Fare            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Fare işaretçisi    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Fare tekerleği      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| İlerleme durumu         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Dokunma            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`dokunmaya duyarlı bir cihazda tek bir temas noktasını temsil eder. |

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [ASP.NET Core referans kaynağında EventArgs sınıfları (dotnet/aspnetcore release/3.1 şube)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN web dokümanları: GlobalEventHandlers,](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; HTML öğelerinin her DOM olayını desteklediği bilgileri içerir.

## <a name="lambda-expressions"></a>Lambda ifadeleri

[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Bir dizi öğe üzerinde yinelediğinizde olduğu gibi ek değerlerin üzerinden kapanmak genellikle uygundur. Aşağıdaki örnek, her biri bir olay `UpdateHeading` bağımsız değişkenini`MouseEventArgs`( ) ve`buttonNumber`ui'de seçildiğinde onun düğme numarasını ( ) geçen çağrıları çağıran üç düğme oluşturur:

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
> Döngü değişkenini (`i`) `for` doğrudan bir lambda ifadesinde bir döngüde **kullanmayın.** Aksi takdirde aynı değişken tüm lambdas `i`'değeri tüm lambdas aynı olması neden tüm lambda ifadeler tarafından kullanılır. Değerini her zaman yerel bir`buttonNumber` değişkende (önceki örnekte) yakalayın ve sonra kullanın.

## <a name="eventcallback"></a>OlayCallback

İç içe bileşenleri ile ortak bir senaryo bir alt bileşen olay örneğin,&mdash;bir `onclick` olay çocuk oluşursa bir üst bileşenin yöntemini çalıştırmak için istek. Bileşenler arasında olayları ortaya `EventCallback`çıkarmak için bir . Bir üst bileşen, bir alt bileşenin.'ine `EventCallback`geri arama yöntemi atayabilir.

Örnek `ChildComponent` uygulamada *(Components/ChildComponent.razor)* bir düğmenin `onclick` işleyicisinin numunenin .'sinden `EventCallback` `ParentComponent`bir temsilci almak üzere nasıl ayarlanır olduğunu gösterir. Çevre `EventCallback` birimi aygıtından bir `MouseEventArgs` `onclick` olay için uygun olan ile yazılır:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Çocuğun `ParentComponent` `EventCallback<T>` (`OnClickCallback`) yöntemine `ShowMessage` göre ayarlar.

*Sayfalar/ParentComponent.razor*:

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

Düğme `ChildComponent`seçildiğinde:

* 'nin `ParentComponent` `ShowMessage` yöntemi denir. `_messageText`güncellenir ve `ParentComponent`görüntülenir.
* [StateHasChanged](xref:blazor/lifecycle#state-changes) için bir çağrı geri arama yönteminde gerekli`ShowMessage`değildir ( ). `StateHasChanged`alt olaylar, alt olay `ParentComponent`işleyicilerinde, alt olay işleyicilerinde bileşenin yeniden işlenmesini tetiklediği gibi, otomatik olarak yeniden işlemek için çağrılır.

`EventCallback`ve `EventCallback<T>` eşzamanlı delegelere izin verir. `EventCallback<T>`güçlü bir şekilde yazılır ve belirli bir bağımsız değişken türü gerektirir. `EventCallback`zayıf bir şekilde yazılır ve herhangi bir bağımsız değişken türüne izin verir.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Bir `EventCallback` veya `EventCallback<T>` `InvokeAsync` ile çağırmak <xref:System.Threading.Tasks.Task>ve bekliyor:

```csharp
await callback.InvokeAsync(arg);
```

Kullanım `EventCallback` `EventCallback<T>` ve olay işleme ve bileşen parametreleri bağlama için.

Güçlü bir şekilde `EventCallback<T>` yazılanları tercih `EventCallback`edin. `EventCallback<T>`bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar. Diğer Kullanıcı Bira Etkinliği işleyicilerine benzer şekilde, olay parametresini belirtmek isteğe bağlıdır. Geri `EventCallback` arama için geçirilen bir değer olmadığında kullanın.

## <a name="prevent-default-actions"></a>Varsayılan eylemleri önleme

Bir [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) olay için varsayılan eylemi önlemek için yönerge özniteliğini kullanın.

Giriş aygıtında bir anahtar seçildiğinde ve eleman odağı metin kutusuna geldiğinde, tarayıcı normalde anahtarın karakterini metin kutusunda görüntüler. Aşağıdaki örnekte, `@onkeypress:preventDefault` yönerge özniteliği belirtilerek varsayılan davranış önlenir. Sayaç artışları ve **+** anahtar öğenin değerine `<input>` yakalanmaz:

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

Değer olmadan `@on{EVENT}:preventDefault` öznitelik belirtilmesi `@on{EVENT}:preventDefault="true"`.

Özniteliğin değeri de bir ifade olabilir. Aşağıdaki örnekte, `_shouldPreventDefault` bir `bool` alan ya `true` `false`da ayarlanır:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Varsayılan eylemi önlemek için bir olay işleyicisi gerekli değildir. Olay işleyicisi ve varsayılan eylem senaryoları önlemek bağımsız olarak kullanılabilir.

## <a name="stop-event-propagation"></a>Olay yayılmasını durdur

Olay [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) yayılmasını durdurmak için yönerge özniteliğini kullanın.

Aşağıdaki örnekte, onay kutusunu n seçilmesi ikinci alt `<div>` çocuğun tıklama olaylarının `<div>`üst öğeye yayılmasını önler:

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
