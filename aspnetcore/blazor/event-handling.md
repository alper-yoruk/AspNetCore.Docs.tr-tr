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
ms.openlocfilehash: aa338bbe61eec14bc1e1b3606e11e26bfb0e6a09
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967473"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="0d9da-103">ASP.NET Core Blazor olay işleme</span><span class="sxs-lookup"><span data-stu-id="0d9da-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="0d9da-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="0d9da-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0d9da-105">Razor bileşenleri olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d9da-105">Razor components provide event handling features.</span></span> <span data-ttu-id="0d9da-106">Temsilci türü belirtilmiş bir değer ile [`@on{EVENT}`](xref:mvc/views/razor#onevent) ADLı bir HTML öğesi `@onclick`özniteliği için (örneğin,), Razor bileşeni öznitelik değerini bir olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="0d9da-107">Aşağıdaki kod, Kullanıcı arabiriminde `UpdateHeading` düğme seçildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="0d9da-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="0d9da-108">Aşağıdaki kod, Kullanıcı arabiriminde `CheckChanged` onay kutusu değiştirildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="0d9da-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="0d9da-109">Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="0d9da-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="0d9da-110">[Statehaschanged](xref:blazor/lifecycle#state-changes)el ile çağırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0d9da-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="0d9da-111">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="0d9da-112">Aşağıdaki örnekte, `UpdateHeading` düğme seçildiğinde zaman uyumsuz olarak çağrılır:</span><span class="sxs-lookup"><span data-stu-id="0d9da-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="0d9da-113">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="0d9da-113">Event argument types</span></span>

<span data-ttu-id="0d9da-114">Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="0d9da-115">Yöntem çağrısında bir olay türü belirtmek yalnızca, olay türü yönteminde kullanılıyorsa gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="0d9da-116">Desteklenir `EventArgs` , aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="0d9da-117">Olay</span><span class="sxs-lookup"><span data-stu-id="0d9da-117">Event</span></span>            | <span data-ttu-id="0d9da-118">Sınıf</span><span class="sxs-lookup"><span data-stu-id="0d9da-118">Class</span></span>                | <span data-ttu-id="0d9da-119">DOM olayları ve notları</span><span class="sxs-lookup"><span data-stu-id="0d9da-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="0d9da-120">Pano</span><span class="sxs-lookup"><span data-stu-id="0d9da-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="0d9da-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="0d9da-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="0d9da-122">Sürükleyin</span><span class="sxs-lookup"><span data-stu-id="0d9da-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="0d9da-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="0d9da-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="0d9da-124">`DataTransfer`ve `DataTransferItem` öğe verilerini sürüklemiş tutun.</span><span class="sxs-lookup"><span data-stu-id="0d9da-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="0d9da-125">Hata</span><span class="sxs-lookup"><span data-stu-id="0d9da-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="0d9da-126">Olay</span><span class="sxs-lookup"><span data-stu-id="0d9da-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="0d9da-127">*Genel*</span><span class="sxs-lookup"><span data-stu-id="0d9da-127">*General*</span></span><br><span data-ttu-id="0d9da-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="0d9da-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="0d9da-129">*Yapıştırır*</span><span class="sxs-lookup"><span data-stu-id="0d9da-129">*Clipboard*</span></span><br><span data-ttu-id="0d9da-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="0d9da-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="0d9da-131">*Giriş*</span><span class="sxs-lookup"><span data-stu-id="0d9da-131">*Input*</span></span><br><span data-ttu-id="0d9da-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="0d9da-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="0d9da-133">*Medya*</span><span class="sxs-lookup"><span data-stu-id="0d9da-133">*Media*</span></span><br><span data-ttu-id="0d9da-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="0d9da-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="0d9da-135">Çı</span><span class="sxs-lookup"><span data-stu-id="0d9da-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="0d9da-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="0d9da-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="0d9da-137">İçin `relatedTarget`destek içermez.</span><span class="sxs-lookup"><span data-stu-id="0d9da-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="0d9da-138">Giriş</span><span class="sxs-lookup"><span data-stu-id="0d9da-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="0d9da-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="0d9da-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="0d9da-140">Klavye</span><span class="sxs-lookup"><span data-stu-id="0d9da-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="0d9da-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="0d9da-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="0d9da-142">Fare</span><span class="sxs-lookup"><span data-stu-id="0d9da-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="0d9da-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="0d9da-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="0d9da-144">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="0d9da-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="0d9da-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="0d9da-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="0d9da-146">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="0d9da-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="0d9da-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="0d9da-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="0d9da-148">İlerleme durumu</span><span class="sxs-lookup"><span data-stu-id="0d9da-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="0d9da-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="0d9da-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="0d9da-150">Dokunma</span><span class="sxs-lookup"><span data-stu-id="0d9da-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="0d9da-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="0d9da-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="0d9da-152">`TouchPoint`dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="0d9da-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="0d9da-153">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="0d9da-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="0d9da-154">[ASP.NET Core başvuru kaynağındaki EventArgs sınıfları (DotNet/aspnetcore sürümü/3.1 dalı)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="0d9da-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="0d9da-155">[MDN Web belgeleri: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; , hangi HTML öğelerinin her Dom olayını destekledikleri hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="0d9da-156">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="0d9da-156">Lambda expressions</span></span>

<span data-ttu-id="0d9da-157">[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="0d9da-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="0d9da-158">Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="0d9da-159">Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni (`MouseEventArgs`) ve düğme numarası (`buttonNumber`) geçiren üç düğme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0d9da-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="0d9da-160">Döngü değişkenini (`i`) bir `for` döngüde doğrudan bir lambda ifadesinde kullanmayın. **not**</span><span class="sxs-lookup"><span data-stu-id="0d9da-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="0d9da-161">Aksi halde, tüm lambda ifadeleri tarafından değeri tüm Lambdalar aynı olmasına neden olan `i`değişken aynı değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d9da-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="0d9da-162">Her zaman değerini yerel bir değişkende (`buttonNumber` önceki örnekte) yakalayın ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d9da-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="0d9da-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="0d9da-163">EventCallback</span></span>

<span data-ttu-id="0d9da-164">İç içe bileşenler içeren yaygın bir senaryo, alt bileşen olayı&mdash;olduğunda bir üst bileşenin yöntemini, örneğin bir `onclick` olay gerçekleştiğinde bir olay oluştuğunda çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="0d9da-165">Olayları bileşenler genelinde göstermek için bir `EventCallback`kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d9da-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="0d9da-166">Bir üst bileşen bir alt bileşene geri çağırma yöntemi atayabilir `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="0d9da-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="0d9da-167">Örnek `ChildComponent` uygulamada (*Bileşenler/childcomponent. Razor*), bir düğmenin `onclick` işleyicisinin örnekten bir `EventCallback` temsilci alacak şekilde nasıl ayarlandığını gösterir. `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="0d9da-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="0d9da-168">`EventCallback` ,, Bir çevre `MouseEventArgs`cihazından bir `onclick` olay için uygun olan ile öğesine yazılır:</span><span class="sxs-lookup"><span data-stu-id="0d9da-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="0d9da-169">, `ParentComponent` Alt öğenin `EventCallback<T>` (`OnClickCallback`) metodunu kendi `ShowMessage` yöntemine ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0d9da-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="0d9da-170">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="0d9da-170">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="0d9da-171">Düğme ' de seçildiğinde `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="0d9da-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="0d9da-172">`ParentComponent`Öğesinin `ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0d9da-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="0d9da-173">`messageText`güncelleştirilir ve içinde görüntülenir `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="0d9da-173">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="0d9da-174">Geri çağırma yönteminde (`ShowMessage`) [statehaschanged](xref:blazor/lifecycle#state-changes) çağrısı gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="0d9da-175">`StateHasChanged`alt olaylar, alt öğe içinde yürütülen `ParentComponent`olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0d9da-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="0d9da-176">`EventCallback`ve `EventCallback<T>` zaman uyumsuz temsilcilere izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="0d9da-177">`EventCallback<T>`kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="0d9da-178">`EventCallback`zayıf ve bağımsız değişken türüne izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="0d9da-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="0d9da-179">Bir `EventCallback` veya `EventCallback<T>` ile `InvokeAsync` çağırın ve şunu bekler <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="0d9da-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="0d9da-180">Olay `EventCallback` işleme `EventCallback<T>` ve bağlama bileşeni parametreleri için ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d9da-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="0d9da-181">Kesin olarak belirlenmiş `EventCallback<T>` türü tercih `EventCallback`edin.</span><span class="sxs-lookup"><span data-stu-id="0d9da-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="0d9da-182">`EventCallback<T>`bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d9da-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="0d9da-183">Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="0d9da-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="0d9da-184">Geri `EventCallback` çağırmaya hiçbir değer geçirilmemişse kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d9da-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="0d9da-185">Varsayılan eylemleri engelle</span><span class="sxs-lookup"><span data-stu-id="0d9da-185">Prevent default actions</span></span>

<span data-ttu-id="0d9da-186">Bir olayın [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) varsayılan eylemini engellemek için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d9da-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="0d9da-187">Giriş cihazında bir anahtar seçildiğinde ve öğe odağı bir metin kutusunda olduğunda, bir tarayıcı normalde metin kutusunda anahtarın karakterini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0d9da-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="0d9da-188">Aşağıdaki örnekte, `@onkeypress:preventDefault` Directive özniteliği belirtilerek varsayılan davranış engellenir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="0d9da-189">Sayaç artar ve **+** anahtar `<input>` öğenin değerine yakalanmaz:</span><span class="sxs-lookup"><span data-stu-id="0d9da-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="0d9da-190">`@on{EVENT}:preventDefault` Özniteliği bir değer olmadan belirtmek ile `@on{EVENT}:preventDefault="true"`eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="0d9da-191">Özniteliğin değeri de bir ifade olabilir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="0d9da-192">`shouldPreventDefault` Aşağıdaki örnekte, `bool` ya `true` `false`da olarak ayarlanan bir alandır:</span><span class="sxs-lookup"><span data-stu-id="0d9da-192">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="0d9da-193">Varsayılan eylemi engellemek için bir olay işleyicisi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="0d9da-194">Olay işleyicisi ve varsayılan eylem senaryolarına bağımsız olarak bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d9da-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="0d9da-195">Olay yaymayı durdur</span><span class="sxs-lookup"><span data-stu-id="0d9da-195">Stop event propagation</span></span>

<span data-ttu-id="0d9da-196">Olay yaymayı [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) durdurmak için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d9da-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="0d9da-197">Aşağıdaki örnekte, onay kutusunun seçilmesi ikinci alt `<div>` öğeden üst `<div>`öğeye yayılan olay tıklamasını önler:</span><span class="sxs-lookup"><span data-stu-id="0d9da-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
