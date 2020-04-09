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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="c0374-103">ASP.NET Core Blazor olay işleme</span><span class="sxs-lookup"><span data-stu-id="c0374-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="c0374-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c0374-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c0374-105">Jilet bileşenleri olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="c0374-105">Razor components provide event handling features.</span></span> <span data-ttu-id="c0374-106">Temsilci türünde bir [`@on{EVENT}`](xref:mvc/views/razor#onevent) değere sahip `@onclick`(örneğin), adlandırılmış bir HTML öğesi için, Bir Jilet bileşeni özniteliğin değerini olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="c0374-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="c0374-107">Aşağıdaki kod, `UpdateHeading` ui'de düğme seçildiğinde yöntemi çağırır:</span><span class="sxs-lookup"><span data-stu-id="c0374-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="c0374-108">Aşağıdaki kod, `CheckChanged` arama-bilgili kutu değiştirildiğinde yöntemi çağırır:</span><span class="sxs-lookup"><span data-stu-id="c0374-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c0374-109">Olay işleyicileri de asynchronous olabilir <xref:System.Threading.Tasks.Task>ve bir .</span><span class="sxs-lookup"><span data-stu-id="c0374-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c0374-110">[StateHasChanged'i](xref:blazor/lifecycle#state-changes)el ile aramanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="c0374-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="c0374-111">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c0374-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c0374-112">Aşağıdaki örnekte, `UpdateHeading` düğme seçildiğinde eş senkronize olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="c0374-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="c0374-113">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="c0374-113">Event argument types</span></span>

<span data-ttu-id="c0374-114">Bazı olaylar için olay bağımsız değişken türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="c0374-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c0374-115">Yöntem çağrısında bir olay türü belirtilmesi yalnızca yöntemde olay türü kullanılıyorsa gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c0374-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="c0374-116">Desteklenenler `EventArgs` aşağıdaki tabloda gösterilir.</span><span class="sxs-lookup"><span data-stu-id="c0374-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="c0374-117">Olay</span><span class="sxs-lookup"><span data-stu-id="c0374-117">Event</span></span>            | <span data-ttu-id="c0374-118">Sınıf</span><span class="sxs-lookup"><span data-stu-id="c0374-118">Class</span></span>                | <span data-ttu-id="c0374-119">DOM etkinlikleri ve notları</span><span class="sxs-lookup"><span data-stu-id="c0374-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="c0374-120">Pano</span><span class="sxs-lookup"><span data-stu-id="c0374-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="c0374-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c0374-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c0374-122">Sürükleyin</span><span class="sxs-lookup"><span data-stu-id="c0374-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="c0374-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c0374-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c0374-124">`DataTransfer`ve `DataTransferItem` sürüklenen öğe verilerini tutun.</span><span class="sxs-lookup"><span data-stu-id="c0374-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="c0374-125">Hata</span><span class="sxs-lookup"><span data-stu-id="c0374-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="c0374-126">Olay</span><span class="sxs-lookup"><span data-stu-id="c0374-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="c0374-127">*Genel*</span><span class="sxs-lookup"><span data-stu-id="c0374-127">*General*</span></span><br><span data-ttu-id="c0374-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c0374-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c0374-129">*Pano*</span><span class="sxs-lookup"><span data-stu-id="c0374-129">*Clipboard*</span></span><br><span data-ttu-id="c0374-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c0374-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c0374-131">*Girdi*</span><span class="sxs-lookup"><span data-stu-id="c0374-131">*Input*</span></span><br><span data-ttu-id="c0374-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c0374-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c0374-133">*Medya*</span><span class="sxs-lookup"><span data-stu-id="c0374-133">*Media*</span></span><br><span data-ttu-id="c0374-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c0374-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="c0374-135">Odak</span><span class="sxs-lookup"><span data-stu-id="c0374-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="c0374-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c0374-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c0374-137">Destek `relatedTarget`içermez.</span><span class="sxs-lookup"><span data-stu-id="c0374-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c0374-138">Girdi</span><span class="sxs-lookup"><span data-stu-id="c0374-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="c0374-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c0374-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c0374-140">Klavye</span><span class="sxs-lookup"><span data-stu-id="c0374-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="c0374-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c0374-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c0374-142">Fare</span><span class="sxs-lookup"><span data-stu-id="c0374-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="c0374-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c0374-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c0374-144">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="c0374-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="c0374-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c0374-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c0374-146">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="c0374-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="c0374-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c0374-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c0374-148">İlerleme durumu</span><span class="sxs-lookup"><span data-stu-id="c0374-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="c0374-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c0374-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c0374-150">Dokunma</span><span class="sxs-lookup"><span data-stu-id="c0374-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="c0374-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c0374-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c0374-152">`TouchPoint`dokunmaya duyarlı bir cihazda tek bir temas noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="c0374-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c0374-153">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="c0374-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="c0374-154">[ASP.NET Core referans kaynağında EventArgs sınıfları (dotnet/aspnetcore release/3.1 şube)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="c0374-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="c0374-155">[MDN web dokümanları: GlobalEventHandlers,](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; HTML öğelerinin her DOM olayını desteklediği bilgileri içerir.</span><span class="sxs-lookup"><span data-stu-id="c0374-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="c0374-156">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="c0374-156">Lambda expressions</span></span>

<span data-ttu-id="c0374-157">[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c0374-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c0374-158">Bir dizi öğe üzerinde yinelediğinizde olduğu gibi ek değerlerin üzerinden kapanmak genellikle uygundur.</span><span class="sxs-lookup"><span data-stu-id="c0374-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c0374-159">Aşağıdaki örnek, her biri bir olay `UpdateHeading` bağımsız değişkenini`MouseEventArgs`( ) ve`buttonNumber`ui'de seçildiğinde onun düğme numarasını ( ) geçen çağrıları çağıran üç düğme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c0374-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="c0374-160">Döngü değişkenini (`i`) `for` doğrudan bir lambda ifadesinde bir döngüde **kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="c0374-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="c0374-161">Aksi takdirde aynı değişken tüm lambdas `i`'değeri tüm lambdas aynı olması neden tüm lambda ifadeler tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0374-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="c0374-162">Değerini her zaman yerel bir`buttonNumber` değişkende (önceki örnekte) yakalayın ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0374-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="c0374-163">OlayCallback</span><span class="sxs-lookup"><span data-stu-id="c0374-163">EventCallback</span></span>

<span data-ttu-id="c0374-164">İç içe bileşenleri ile ortak bir senaryo bir alt bileşen olay örneğin,&mdash;bir `onclick` olay çocuk oluşursa bir üst bileşenin yöntemini çalıştırmak için istek.</span><span class="sxs-lookup"><span data-stu-id="c0374-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="c0374-165">Bileşenler arasında olayları ortaya `EventCallback`çıkarmak için bir .</span><span class="sxs-lookup"><span data-stu-id="c0374-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="c0374-166">Bir üst bileşen, bir alt bileşenin.'ine `EventCallback`geri arama yöntemi atayabilir.</span><span class="sxs-lookup"><span data-stu-id="c0374-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="c0374-167">Örnek `ChildComponent` uygulamada *(Components/ChildComponent.razor)* bir düğmenin `onclick` işleyicisinin numunenin .'sinden `EventCallback` `ParentComponent`bir temsilci almak üzere nasıl ayarlanır olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="c0374-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c0374-168">Çevre `EventCallback` birimi aygıtından bir `MouseEventArgs` `onclick` olay için uygun olan ile yazılır:</span><span class="sxs-lookup"><span data-stu-id="c0374-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c0374-169">Çocuğun `ParentComponent` `EventCallback<T>` (`OnClickCallback`) yöntemine `ShowMessage` göre ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c0374-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="c0374-170">*Sayfalar/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="c0374-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="c0374-171">Düğme `ChildComponent`seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="c0374-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c0374-172">'nin `ParentComponent` `ShowMessage` yöntemi denir.</span><span class="sxs-lookup"><span data-stu-id="c0374-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c0374-173">`_messageText`güncellenir ve `ParentComponent`görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0374-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c0374-174">[StateHasChanged](xref:blazor/lifecycle#state-changes) için bir çağrı geri arama yönteminde gerekli`ShowMessage`değildir ( ).</span><span class="sxs-lookup"><span data-stu-id="c0374-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c0374-175">`StateHasChanged`alt olaylar, alt olay `ParentComponent`işleyicilerinde, alt olay işleyicilerinde bileşenin yeniden işlenmesini tetiklediği gibi, otomatik olarak yeniden işlemek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c0374-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c0374-176">`EventCallback`ve `EventCallback<T>` eşzamanlı delegelere izin verir.</span><span class="sxs-lookup"><span data-stu-id="c0374-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="c0374-177">`EventCallback<T>`güçlü bir şekilde yazılır ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c0374-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c0374-178">`EventCallback`zayıf bir şekilde yazılır ve herhangi bir bağımsız değişken türüne izin verir.</span><span class="sxs-lookup"><span data-stu-id="c0374-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="c0374-179">Bir `EventCallback` veya `EventCallback<T>` `InvokeAsync` ile çağırmak <xref:System.Threading.Tasks.Task>ve bekliyor:</span><span class="sxs-lookup"><span data-stu-id="c0374-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c0374-180">Kullanım `EventCallback` `EventCallback<T>` ve olay işleme ve bileşen parametreleri bağlama için.</span><span class="sxs-lookup"><span data-stu-id="c0374-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="c0374-181">Güçlü bir şekilde `EventCallback<T>` yazılanları tercih `EventCallback`edin.</span><span class="sxs-lookup"><span data-stu-id="c0374-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="c0374-182">`EventCallback<T>`bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c0374-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="c0374-183">Diğer Kullanıcı Bira Etkinliği işleyicilerine benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="c0374-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c0374-184">Geri `EventCallback` arama için geçirilen bir değer olmadığında kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0374-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="c0374-185">Varsayılan eylemleri önleme</span><span class="sxs-lookup"><span data-stu-id="c0374-185">Prevent default actions</span></span>

<span data-ttu-id="c0374-186">Bir [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) olay için varsayılan eylemi önlemek için yönerge özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0374-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="c0374-187">Giriş aygıtında bir anahtar seçildiğinde ve eleman odağı metin kutusuna geldiğinde, tarayıcı normalde anahtarın karakterini metin kutusunda görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0374-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="c0374-188">Aşağıdaki örnekte, `@onkeypress:preventDefault` yönerge özniteliği belirtilerek varsayılan davranış önlenir.</span><span class="sxs-lookup"><span data-stu-id="c0374-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="c0374-189">Sayaç artışları ve **+** anahtar öğenin değerine `<input>` yakalanmaz:</span><span class="sxs-lookup"><span data-stu-id="c0374-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="c0374-190">Değer olmadan `@on{EVENT}:preventDefault` öznitelik belirtilmesi `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="c0374-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="c0374-191">Özniteliğin değeri de bir ifade olabilir.</span><span class="sxs-lookup"><span data-stu-id="c0374-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="c0374-192">Aşağıdaki örnekte, `_shouldPreventDefault` bir `bool` alan ya `true` `false`da ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="c0374-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="c0374-193">Varsayılan eylemi önlemek için bir olay işleyicisi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c0374-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="c0374-194">Olay işleyicisi ve varsayılan eylem senaryoları önlemek bağımsız olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c0374-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="c0374-195">Olay yayılmasını durdur</span><span class="sxs-lookup"><span data-stu-id="c0374-195">Stop event propagation</span></span>

<span data-ttu-id="c0374-196">Olay [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) yayılmasını durdurmak için yönerge özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0374-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="c0374-197">Aşağıdaki örnekte, onay kutusunu n seçilmesi ikinci alt `<div>` çocuğun tıklama olaylarının `<div>`üst öğeye yayılmasını önler:</span><span class="sxs-lookup"><span data-stu-id="c0374-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
