---
title: ASP.NET Core Blazor olay işleme
author: guardrex
description: Olay Blazor bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 32f7595cffc2c31116c8d876c9f9526b84c52f14
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103888"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="a3e7a-103">ASP.NET Core Blazor olay işleme</span><span class="sxs-lookup"><span data-stu-id="a3e7a-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="a3e7a-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="a3e7a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="a3e7a-105">bileşenler olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-105"> components provide event handling features.</span></span> <span data-ttu-id="a3e7a-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent)Temsilci türü belirtilmiş bir değer ile adlı BIR HTML öğesi özniteliği için (örneğin, `@onclick` ), bir Razor bileşen öznitelik değerini bir olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="a3e7a-107">Aşağıdaki kod, `UpdateHeading` Kullanıcı arabiriminde düğme seçildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="a3e7a-108">Aşağıdaki kod, `CheckChanged` Kullanıcı arabiriminde onay kutusu değiştirildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="a3e7a-109">Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="a3e7a-110">[Statehaschanged](xref:blazor/components/lifecycle#state-changes)el ile çağırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="a3e7a-111">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="a3e7a-112">Aşağıdaki örnekte, `UpdateHeading` Düğme seçildiğinde zaman uyumsuz olarak çağrılır:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="a3e7a-113">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="a3e7a-113">Event argument types</span></span>

<span data-ttu-id="a3e7a-114">Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="a3e7a-115">Yöntem çağrısında bir olay türü belirtmek yalnızca, olay türü yönteminde kullanılıyorsa gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="a3e7a-116">Desteklenir <xref:System.EventArgs> , aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="a3e7a-117">Olay</span><span class="sxs-lookup"><span data-stu-id="a3e7a-117">Event</span></span>            | <span data-ttu-id="a3e7a-118">Sınıf</span><span class="sxs-lookup"><span data-stu-id="a3e7a-118">Class</span></span>                | <span data-ttu-id="a3e7a-119">DOM olayları ve notları</span><span class="sxs-lookup"><span data-stu-id="a3e7a-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="a3e7a-120">Pano</span><span class="sxs-lookup"><span data-stu-id="a3e7a-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="a3e7a-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="a3e7a-122">Sürükleyin</span><span class="sxs-lookup"><span data-stu-id="a3e7a-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="a3e7a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="a3e7a-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>ve <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> öğe verilerini sürüklemiş tutun.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="a3e7a-125">Hata</span><span class="sxs-lookup"><span data-stu-id="a3e7a-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="a3e7a-126">Olay</span><span class="sxs-lookup"><span data-stu-id="a3e7a-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="a3e7a-127">*Genel*</span><span class="sxs-lookup"><span data-stu-id="a3e7a-127">*General*</span></span><br><span data-ttu-id="a3e7a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="a3e7a-129">*Pano*</span><span class="sxs-lookup"><span data-stu-id="a3e7a-129">*Clipboard*</span></span><br><span data-ttu-id="a3e7a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="a3e7a-131">*Giriş*</span><span class="sxs-lookup"><span data-stu-id="a3e7a-131">*Input*</span></span><br><span data-ttu-id="a3e7a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="a3e7a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="a3e7a-133">*Medya*</span><span class="sxs-lookup"><span data-stu-id="a3e7a-133">*Media*</span></span><br><span data-ttu-id="a3e7a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="a3e7a-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>olay adlarıyla olay bağımsız değişken türleri arasındaki eşlemeleri yapılandırmak için öznitelikleri tutar.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="a3e7a-136">Çı</span><span class="sxs-lookup"><span data-stu-id="a3e7a-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="a3e7a-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="a3e7a-138">İçin destek içermez `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="a3e7a-139">Giriş</span><span class="sxs-lookup"><span data-stu-id="a3e7a-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="a3e7a-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="a3e7a-141">Klavye</span><span class="sxs-lookup"><span data-stu-id="a3e7a-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="a3e7a-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="a3e7a-143">Fare</span><span class="sxs-lookup"><span data-stu-id="a3e7a-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="a3e7a-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="a3e7a-145">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="a3e7a-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="a3e7a-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="a3e7a-147">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="a3e7a-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="a3e7a-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="a3e7a-149">İlerleme Durumu</span><span class="sxs-lookup"><span data-stu-id="a3e7a-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="a3e7a-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="a3e7a-151">Dokunma</span><span class="sxs-lookup"><span data-stu-id="a3e7a-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="a3e7a-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="a3e7a-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="a3e7a-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="a3e7a-154">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="a3e7a-155">[ASP.NET Core başvuru kaynağındaki EventArgs sınıfları (DotNet/aspnetcore sürümü/3.1 dalı)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="a3e7a-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="a3e7a-156">[MDN Web belgeleri: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): hangi HTML ÖĞELERININ her Dom olayını destekledikleri hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="a3e7a-157">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="a3e7a-157">Lambda expressions</span></span>

<span data-ttu-id="a3e7a-158">[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="a3e7a-159">Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="a3e7a-160">Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) ve düğme numarası () geçiren üç düğme oluşturur `buttonNumber` :</span><span class="sxs-lookup"><span data-stu-id="a3e7a-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="a3e7a-161">Döngü **not** değişkenini, `i` önceki `for` döngü örneğinde veya bir döngüde başvuru değişkeni gibi bir lambda ifadesinde doğrudan kullanmayın `foreach` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="a3e7a-162">Aksi halde, aynı değişken tüm lambda ifadeleri tarafından kullanılır ve tüm Lambdalar için aynı değerin kullanılmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="a3e7a-163">Değişkenin değerini her zaman yerel bir değişkende yakala ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="a3e7a-164">Önceki örnekte, Loop değişkeni `i` öğesine atanır `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="a3e7a-165">EventCallback</span><span class="sxs-lookup"><span data-stu-id="a3e7a-165">EventCallback</span></span>

<span data-ttu-id="a3e7a-166">İç içe bileşenler içeren yaygın bir senaryo, bir alt bileşen olayı gerçekleştiğinde bir üst bileşenin yöntemini çalıştırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="a3e7a-167">`onclick`Alt bileşende gerçekleşen bir olay yaygın kullanım durumdur.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="a3e7a-168">Olayları bileşenler genelinde göstermek için bir kullanın <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="a3e7a-169">Bir üst bileşen bir alt bileşene geri çağırma yöntemi atayabilir <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="a3e7a-170">`ChildComponent`Örnek uygulamada (*Bileşenler/childcomponent. Razor*), bir düğmenin `onclick` işleyicisinin örnekten bir temsilci alacak şekilde nasıl ayarlandığını gösterir <xref:Microsoft.AspNetCore.Components.EventCallback> `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="a3e7a-171">,, <xref:Microsoft.AspNetCore.Components.EventCallback> `MouseEventArgs` `onclick` Bir çevre cihazından bir olay için uygun olan ile öğesine yazılır:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="a3e7a-172">, `ParentComponent` Alt öğenin <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) metodunu kendi yöntemine ayarlar `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="a3e7a-173">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="a3e7a-174">Düğme ' de seçildiğinde `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="a3e7a-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="a3e7a-175">`ParentComponent`Öğesinin `ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="a3e7a-176">`messageText`güncelleştirilir ve içinde görüntülenir `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="a3e7a-177">Geri çağırma yönteminde () [Statehaschanged](xref:blazor/components/lifecycle#state-changes) çağrısı gerekli değildir `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-177">A call to [StateHasChanged](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="a3e7a-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>alt `ParentComponent` Olaylar, alt öğe içinde yürütülen olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="a3e7a-179"><xref:Microsoft.AspNetCore.Components.EventCallback>ve <xref:Microsoft.AspNetCore.Components.EventCallback%601> zaman uyumsuz temsilcilere izin verir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="a3e7a-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601>kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="a3e7a-181"><xref:Microsoft.AspNetCore.Components.EventCallback>zayıf ve bağımsız değişken türüne izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="a3e7a-182">Bir <xref:Microsoft.AspNetCore.Components.EventCallback> veya <xref:Microsoft.AspNetCore.Components.EventCallback%601> ile çağırın <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ve şunu bekler <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="a3e7a-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="a3e7a-183"><xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> Olay işleme ve bağlama bileşeni parametreleri için ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="a3e7a-184">Kesin olarak belirlenmiş türü tercih edin <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="a3e7a-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601>bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="a3e7a-186">Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="a3e7a-187"><xref:Microsoft.AspNetCore.Components.EventCallback>Geri çağırmaya hiçbir değer geçirilmemişse kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="a3e7a-188">Varsayılan eylemleri engelle</span><span class="sxs-lookup"><span data-stu-id="a3e7a-188">Prevent default actions</span></span>

<span data-ttu-id="a3e7a-189">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="a3e7a-190">Giriş cihazında bir anahtar seçildiğinde ve öğe odağı bir metin kutusunda olduğunda, bir tarayıcı normalde metin kutusunda anahtarın karakterini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="a3e7a-191">Aşağıdaki örnekte, Directive özniteliği belirtilerek varsayılan davranış engellenir `@onkeypress:preventDefault` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="a3e7a-192">Sayaç artar ve **+** anahtar `<input>` öğenin değerine yakalanmaz:</span><span class="sxs-lookup"><span data-stu-id="a3e7a-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="a3e7a-193">`@on{EVENT}:preventDefault`Özniteliği bir değer olmadan belirtmek ile eşdeğerdir `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="a3e7a-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="a3e7a-194">Özniteliğin değeri de bir ifade olabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="a3e7a-195">Aşağıdaki örnekte, `shouldPreventDefault` `bool` ya da olarak ayarlanan bir alandır `true` `false` :</span><span class="sxs-lookup"><span data-stu-id="a3e7a-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="a3e7a-196">Varsayılan eylemi engellemek için bir olay işleyicisi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="a3e7a-197">Olay işleyicisi ve varsayılan eylem senaryolarına bağımsız olarak bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="a3e7a-198">Olay yaymayı durdur</span><span class="sxs-lookup"><span data-stu-id="a3e7a-198">Stop event propagation</span></span>

<span data-ttu-id="a3e7a-199">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Olay yaymayı durdurmak için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3e7a-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="a3e7a-200">Aşağıdaki örnekte, onay kutusunun seçilmesi ikinci alt öğeden üst öğeye yayılan olay tıklamasını önler `<div>` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="a3e7a-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
