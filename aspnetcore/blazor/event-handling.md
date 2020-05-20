---
title: ASP.NET Core Blazor olay işleme
author: guardrex
description: Olay Blazor bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin.
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
ms.openlocfilehash: 610cb9124f59ed07f1fe6193f92052b4513450c8
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424246"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="47f61-103">ASP.NET Core Blazor olay işleme</span><span class="sxs-lookup"><span data-stu-id="47f61-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="47f61-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="47f61-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="47f61-105">Razor bileşenleri olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="47f61-105">Razor components provide event handling features.</span></span> <span data-ttu-id="47f61-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent)Temsilci türü belirtilmiş bir değer ile adlı BIR HTML öğesi özniteliği için (örneğin, `@onclick` ), Razor bileşeni öznitelik değerini bir olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="47f61-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="47f61-107">Aşağıdaki kod, `UpdateHeading` Kullanıcı arabiriminde düğme seçildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="47f61-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="47f61-108">Aşağıdaki kod, `CheckChanged` Kullanıcı arabiriminde onay kutusu değiştirildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="47f61-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="47f61-109">Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="47f61-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="47f61-110">[Statehaschanged](xref:blazor/lifecycle#state-changes)el ile çağırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="47f61-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="47f61-111">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="47f61-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="47f61-112">Aşağıdaki örnekte, `UpdateHeading` Düğme seçildiğinde zaman uyumsuz olarak çağrılır:</span><span class="sxs-lookup"><span data-stu-id="47f61-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="47f61-113">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="47f61-113">Event argument types</span></span>

<span data-ttu-id="47f61-114">Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="47f61-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="47f61-115">Yöntem çağrısında bir olay türü belirtmek yalnızca, olay türü yönteminde kullanılıyorsa gereklidir.</span><span class="sxs-lookup"><span data-stu-id="47f61-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="47f61-116">Desteklenir `EventArgs` , aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="47f61-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="47f61-117">Olay</span><span class="sxs-lookup"><span data-stu-id="47f61-117">Event</span></span>            | <span data-ttu-id="47f61-118">Sınıf</span><span class="sxs-lookup"><span data-stu-id="47f61-118">Class</span></span>                | <span data-ttu-id="47f61-119">DOM olayları ve notları</span><span class="sxs-lookup"><span data-stu-id="47f61-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="47f61-120">Pano</span><span class="sxs-lookup"><span data-stu-id="47f61-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="47f61-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="47f61-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="47f61-122">Sürükleyin</span><span class="sxs-lookup"><span data-stu-id="47f61-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="47f61-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="47f61-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="47f61-124">`DataTransfer`ve `DataTransferItem` öğe verilerini sürüklemiş tutun.</span><span class="sxs-lookup"><span data-stu-id="47f61-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="47f61-125">Hata</span><span class="sxs-lookup"><span data-stu-id="47f61-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="47f61-126">Olay</span><span class="sxs-lookup"><span data-stu-id="47f61-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="47f61-127">*Genel*</span><span class="sxs-lookup"><span data-stu-id="47f61-127">*General*</span></span><br><span data-ttu-id="47f61-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="47f61-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="47f61-129">*Pano*</span><span class="sxs-lookup"><span data-stu-id="47f61-129">*Clipboard*</span></span><br><span data-ttu-id="47f61-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="47f61-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="47f61-131">*Giriş*</span><span class="sxs-lookup"><span data-stu-id="47f61-131">*Input*</span></span><br><span data-ttu-id="47f61-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="47f61-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="47f61-133">*Medya*</span><span class="sxs-lookup"><span data-stu-id="47f61-133">*Media*</span></span><br><span data-ttu-id="47f61-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="47f61-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="47f61-135">Çı</span><span class="sxs-lookup"><span data-stu-id="47f61-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="47f61-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="47f61-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="47f61-137">İçin destek içermez `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="47f61-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="47f61-138">Giriş</span><span class="sxs-lookup"><span data-stu-id="47f61-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="47f61-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="47f61-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="47f61-140">Klavye</span><span class="sxs-lookup"><span data-stu-id="47f61-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="47f61-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="47f61-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="47f61-142">Fare</span><span class="sxs-lookup"><span data-stu-id="47f61-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="47f61-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="47f61-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="47f61-144">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="47f61-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="47f61-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="47f61-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="47f61-146">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="47f61-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="47f61-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="47f61-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="47f61-148">İlerleme Durumu</span><span class="sxs-lookup"><span data-stu-id="47f61-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="47f61-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="47f61-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="47f61-150">Dokunma</span><span class="sxs-lookup"><span data-stu-id="47f61-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="47f61-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="47f61-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="47f61-152">`TouchPoint`dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="47f61-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="47f61-153">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="47f61-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="47f61-154">[ASP.NET Core başvuru kaynağındaki EventArgs sınıfları (DotNet/aspnetcore sürümü/3.1 dalı)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="47f61-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="47f61-155">[MDN Web belgeleri: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Hangi HTML öğelerinin her DOM olayını destekledikleri hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="47f61-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="47f61-156">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="47f61-156">Lambda expressions</span></span>

<span data-ttu-id="47f61-157">[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="47f61-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="47f61-158">Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="47f61-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="47f61-159">Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni ( `MouseEventArgs` ) ve düğme numarası () geçiren üç düğme oluşturur `buttonNumber` :</span><span class="sxs-lookup"><span data-stu-id="47f61-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="47f61-160">Döngü **not** değişkenini ( `i` ) bir `for` döngüde doğrudan bir lambda ifadesinde kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="47f61-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="47f61-161">Aksi halde, tüm lambda ifadeleri tarafından `i` değeri tüm Lambdalar aynı olmasına neden olan değişken aynı değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="47f61-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="47f61-162">Her zaman değerini yerel bir değişkende ( `buttonNumber` Önceki örnekte) yakalayın ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="47f61-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="47f61-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="47f61-163">EventCallback</span></span>

<span data-ttu-id="47f61-164">İç içe bileşenler içeren yaygın bir senaryo, bir alt bileşen olayı gerçekleştiğinde bir üst bileşenin yöntemini çalıştırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="47f61-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="47f61-165">`onclick`Alt bileşende gerçekleşen bir olay yaygın kullanım durumdur.</span><span class="sxs-lookup"><span data-stu-id="47f61-165">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="47f61-166">Olayları bileşenler genelinde göstermek için bir kullanın `EventCallback` .</span><span class="sxs-lookup"><span data-stu-id="47f61-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="47f61-167">Bir üst bileşen bir alt bileşene geri çağırma yöntemi atayabilir `EventCallback` .</span><span class="sxs-lookup"><span data-stu-id="47f61-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="47f61-168">`ChildComponent`Örnek uygulamada (*Bileşenler/childcomponent. Razor*), bir düğmenin `onclick` işleyicisinin örnekten bir temsilci alacak şekilde nasıl ayarlandığını gösterir `EventCallback` `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="47f61-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="47f61-169">,, `EventCallback` `MouseEventArgs` `onclick` Bir çevre cihazından bir olay için uygun olan ile öğesine yazılır:</span><span class="sxs-lookup"><span data-stu-id="47f61-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="47f61-170">, `ParentComponent` Alt öğenin `EventCallback<T>` ( `OnClickCallback` ) metodunu kendi yöntemine ayarlar `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="47f61-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="47f61-171">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47f61-171">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="47f61-172">Düğme ' de seçildiğinde `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="47f61-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="47f61-173">`ParentComponent`Öğesinin `ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="47f61-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="47f61-174">`messageText`güncelleştirilir ve içinde görüntülenir `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="47f61-174">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="47f61-175">Geri çağırma yönteminde () [Statehaschanged](xref:blazor/lifecycle#state-changes) çağrısı gerekli değildir `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="47f61-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="47f61-176">`StateHasChanged`alt `ParentComponent` Olaylar, alt öğe içinde yürütülen olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="47f61-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="47f61-177">`EventCallback`ve `EventCallback<T>` zaman uyumsuz temsilcilere izin verir.</span><span class="sxs-lookup"><span data-stu-id="47f61-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="47f61-178">`EventCallback<T>`kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="47f61-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="47f61-179">`EventCallback`zayıf ve bağımsız değişken türüne izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="47f61-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="47f61-180">Bir `EventCallback` veya `EventCallback<T>` ile çağırın `InvokeAsync` ve şunu bekler <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="47f61-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="47f61-181">`EventCallback` `EventCallback<T>` Olay işleme ve bağlama bileşeni parametreleri için ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="47f61-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="47f61-182">Kesin olarak belirlenmiş türü tercih edin `EventCallback<T>` `EventCallback` .</span><span class="sxs-lookup"><span data-stu-id="47f61-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="47f61-183">`EventCallback<T>`bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="47f61-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="47f61-184">Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="47f61-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="47f61-185">`EventCallback`Geri çağırmaya hiçbir değer geçirilmemişse kullanın.</span><span class="sxs-lookup"><span data-stu-id="47f61-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="47f61-186">Varsayılan eylemleri engelle</span><span class="sxs-lookup"><span data-stu-id="47f61-186">Prevent default actions</span></span>

<span data-ttu-id="47f61-187">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="47f61-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="47f61-188">Giriş cihazında bir anahtar seçildiğinde ve öğe odağı bir metin kutusunda olduğunda, bir tarayıcı normalde metin kutusunda anahtarın karakterini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="47f61-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="47f61-189">Aşağıdaki örnekte, Directive özniteliği belirtilerek varsayılan davranış engellenir `@onkeypress:preventDefault` .</span><span class="sxs-lookup"><span data-stu-id="47f61-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="47f61-190">Sayaç artar ve **+** anahtar `<input>` öğenin değerine yakalanmaz:</span><span class="sxs-lookup"><span data-stu-id="47f61-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="47f61-191">`@on{EVENT}:preventDefault`Özniteliği bir değer olmadan belirtmek ile eşdeğerdir `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="47f61-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="47f61-192">Özniteliğin değeri de bir ifade olabilir.</span><span class="sxs-lookup"><span data-stu-id="47f61-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="47f61-193">Aşağıdaki örnekte, `shouldPreventDefault` `bool` ya da olarak ayarlanan bir alandır `true` `false` :</span><span class="sxs-lookup"><span data-stu-id="47f61-193">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="47f61-194">Varsayılan eylemi engellemek için bir olay işleyicisi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="47f61-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="47f61-195">Olay işleyicisi ve varsayılan eylem senaryolarına bağımsız olarak bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="47f61-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="47f61-196">Olay yaymayı durdur</span><span class="sxs-lookup"><span data-stu-id="47f61-196">Stop event propagation</span></span>

<span data-ttu-id="47f61-197">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Olay yaymayı durdurmak için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="47f61-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="47f61-198">Aşağıdaki örnekte, onay kutusunun seçilmesi ikinci alt öğeden üst öğeye yayılan olay tıklamasını önler `<div>` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="47f61-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
