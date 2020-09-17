---
title: ASP.NET Core Blazor olay işleme
author: guardrex
description: Olay Blazor bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: cb7009946f56b3c8f1e5eeabb09b81075749c720
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722455"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="4c0cb-103">ASP.NET Core Blazor olay işleme</span><span class="sxs-lookup"><span data-stu-id="4c0cb-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="4c0cb-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="4c0cb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4c0cb-105">Razor bileşenler olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-105">Razor components provide event handling features.</span></span> <span data-ttu-id="4c0cb-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent)Temsilci türü belirtilmiş bir değer ile adlı BIR HTML öğesi özniteliği için (örneğin, `@onclick` ), bir Razor bileşen öznitelik değerini bir olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="4c0cb-107">Aşağıdaki kod, `UpdateHeading` Kullanıcı arabiriminde düğme seçildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="4c0cb-108">Aşağıdaki kod, `CheckChanged` Kullanıcı arabiriminde onay kutusu değiştirildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="4c0cb-109">Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="4c0cb-110">[Statehaschanged](xref:blazor/components/lifecycle#state-changes)el ile çağırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="4c0cb-111">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="4c0cb-112">Aşağıdaki örnekte, `UpdateHeading` Düğme seçildiğinde zaman uyumsuz olarak çağrılır:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="4c0cb-113">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="4c0cb-113">Event argument types</span></span>

<span data-ttu-id="4c0cb-114">Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="4c0cb-115">Olay yöntemi tanımında bir olay parametresi belirtmek isteğe bağlıdır ve yalnızca yöntemde olay türü kullanılıyorsa gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="4c0cb-116">Aşağıdaki örnekte, `MouseEventArgs` olay bağımsız değişkeni `ShowMessage` yöntemde ileti metnini ayarlamak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="4c0cb-117">Desteklenir <xref:System.EventArgs> , aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="4c0cb-118">Olay</span><span class="sxs-lookup"><span data-stu-id="4c0cb-118">Event</span></span>            | <span data-ttu-id="4c0cb-119">Sınıf</span><span class="sxs-lookup"><span data-stu-id="4c0cb-119">Class</span></span>  | <span data-ttu-id="4c0cb-120">DOM olayları ve notları</span><span class="sxs-lookup"><span data-stu-id="4c0cb-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="4c0cb-121">Pano</span><span class="sxs-lookup"><span data-stu-id="4c0cb-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="4c0cb-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="4c0cb-123">Sürükleyin</span><span class="sxs-lookup"><span data-stu-id="4c0cb-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="4c0cb-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="4c0cb-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> ve <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> öğe verilerini sürüklemiş tutun.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="4c0cb-126">Hata</span><span class="sxs-lookup"><span data-stu-id="4c0cb-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="4c0cb-127">Olay</span><span class="sxs-lookup"><span data-stu-id="4c0cb-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="4c0cb-128">*Genel*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-128">*General*</span></span><br><span data-ttu-id="4c0cb-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="4c0cb-130">*Pano*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-130">*Clipboard*</span></span><br><span data-ttu-id="4c0cb-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="4c0cb-132">*Giriş*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-132">*Input*</span></span><br><span data-ttu-id="4c0cb-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="4c0cb-134">*Medya*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-134">*Media*</span></span><br><span data-ttu-id="4c0cb-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="4c0cb-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> olay adlarıyla olay bağımsız değişken türleri arasındaki eşlemeleri yapılandırmak için öznitelikleri tutar.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="4c0cb-137">Odaklanma</span><span class="sxs-lookup"><span data-stu-id="4c0cb-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="4c0cb-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="4c0cb-139">İçin destek içermez `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="4c0cb-140">Giriş</span><span class="sxs-lookup"><span data-stu-id="4c0cb-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="4c0cb-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="4c0cb-142">Klavye</span><span class="sxs-lookup"><span data-stu-id="4c0cb-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="4c0cb-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="4c0cb-144">Fare</span><span class="sxs-lookup"><span data-stu-id="4c0cb-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="4c0cb-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="4c0cb-146">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="4c0cb-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="4c0cb-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="4c0cb-148">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="4c0cb-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="4c0cb-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="4c0cb-150">İlerleme Durumu</span><span class="sxs-lookup"><span data-stu-id="4c0cb-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="4c0cb-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="4c0cb-152">Dokunma</span><span class="sxs-lookup"><span data-stu-id="4c0cb-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="4c0cb-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="4c0cb-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="4c0cb-155">Olay</span><span class="sxs-lookup"><span data-stu-id="4c0cb-155">Event</span></span>            | <span data-ttu-id="4c0cb-156">Sınıf</span><span class="sxs-lookup"><span data-stu-id="4c0cb-156">Class</span></span> | <span data-ttu-id="4c0cb-157">DOM olayları ve notları</span><span class="sxs-lookup"><span data-stu-id="4c0cb-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="4c0cb-158">Pano</span><span class="sxs-lookup"><span data-stu-id="4c0cb-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="4c0cb-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="4c0cb-160">Sürükleyin</span><span class="sxs-lookup"><span data-stu-id="4c0cb-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="4c0cb-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="4c0cb-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> ve <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> öğe verilerini sürüklemiş tutun.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="4c0cb-163">Hata</span><span class="sxs-lookup"><span data-stu-id="4c0cb-163">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="4c0cb-164">Olay</span><span class="sxs-lookup"><span data-stu-id="4c0cb-164">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="4c0cb-165">*Genel*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-165">*General*</span></span><br><span data-ttu-id="4c0cb-166">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-166">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="4c0cb-167">*Pano*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-167">*Clipboard*</span></span><br><span data-ttu-id="4c0cb-168">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-168">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="4c0cb-169">*Giriş*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-169">*Input*</span></span><br><span data-ttu-id="4c0cb-170">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-170">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="4c0cb-171">*Medya*</span><span class="sxs-lookup"><span data-stu-id="4c0cb-171">*Media*</span></span><br><span data-ttu-id="4c0cb-172">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-172">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="4c0cb-173"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> olay adlarıyla olay bağımsız değişken türleri arasındaki eşlemeleri yapılandırmak için öznitelikleri tutar.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-173"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="4c0cb-174">Odaklanma</span><span class="sxs-lookup"><span data-stu-id="4c0cb-174">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="4c0cb-175">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-175">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="4c0cb-176">İçin destek içermez `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-176">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="4c0cb-177">Giriş</span><span class="sxs-lookup"><span data-stu-id="4c0cb-177">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="4c0cb-178">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-178">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="4c0cb-179">Klavye</span><span class="sxs-lookup"><span data-stu-id="4c0cb-179">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="4c0cb-180">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-180">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="4c0cb-181">Fare</span><span class="sxs-lookup"><span data-stu-id="4c0cb-181">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="4c0cb-182">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-182">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="4c0cb-183">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="4c0cb-183">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="4c0cb-184">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-184">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="4c0cb-185">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="4c0cb-185">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="4c0cb-186">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-186">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="4c0cb-187">İlerleme Durumu</span><span class="sxs-lookup"><span data-stu-id="4c0cb-187">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="4c0cb-188">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-188">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="4c0cb-189">Dokunma</span><span class="sxs-lookup"><span data-stu-id="4c0cb-189">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="4c0cb-190">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="4c0cb-190">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="4c0cb-191"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-191"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="4c0cb-192">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-192">For more information, see the following resources:</span></span>

* <span data-ttu-id="4c0cb-193">[ `EventArgs` ASP.NET Core başvuru kaynağındaki sınıflar (DotNet/aspnetcore `master` dalı)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="4c0cb-193">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="4c0cb-194">`master`Dal, *sonraki* ASP.NET Core sürümü için geliştirme aşamasındaki API 'yi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-194">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="4c0cb-195">Geçerli sürüm için uygun GitHub depo dalını (örneğin, `release/3.1` ) seçin.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-195">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="4c0cb-196">[MDN Web belgeleri: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): hangi HTML ÖĞELERININ her Dom olayını destekledikleri hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-196">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="4c0cb-197">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="4c0cb-197">Lambda expressions</span></span>

<span data-ttu-id="4c0cb-198">[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-198">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="4c0cb-199">Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-199">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="4c0cb-200">Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) ve düğme numarası () geçiren üç düğme oluşturur `buttonNumber` :</span><span class="sxs-lookup"><span data-stu-id="4c0cb-200">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="4c0cb-201">Bir **not** Loop değişkenini, `i` Yukarıdaki döngü örneğinde olduğu gibi doğrudan bir lambda ifadesinde kullanmayın `for` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-201">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="4c0cb-202">Aksi halde, aynı değişken tüm lambda ifadeleri tarafından kullanılır ve tüm Lambdalar için aynı değerin kullanılmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-202">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="4c0cb-203">Değişkenin değerini her zaman yerel bir değişkende yakala ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-203">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="4c0cb-204">Önceki örnekte, Loop değişkeni `i` öğesine atanır `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-204">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="4c0cb-205">EventCallback</span><span class="sxs-lookup"><span data-stu-id="4c0cb-205">EventCallback</span></span>

<span data-ttu-id="4c0cb-206">İç içe bileşenler içeren yaygın bir senaryo, bir alt bileşen olayı gerçekleştiğinde bir üst bileşenin yöntemini çalıştırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-206">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="4c0cb-207">`onclick`Alt bileşende gerçekleşen bir olay yaygın kullanım durumdur.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-207">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="4c0cb-208">Olayları bileşenler genelinde göstermek için bir kullanın <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-208">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="4c0cb-209">Bir üst bileşen bir alt bileşene geri çağırma yöntemi atayabilir <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-209">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="4c0cb-210">`ChildComponent`Örnek uygulamada ( `Components/ChildComponent.razor` ), bir düğmenin `onclick` işleyicisinin örnekten bir temsilci alacak şekilde nasıl ayarlandığını gösterir <xref:Microsoft.AspNetCore.Components.EventCallback> `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-210">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="4c0cb-211">,, <xref:Microsoft.AspNetCore.Components.EventCallback> `MouseEventArgs` `onclick` Bir çevre cihazından bir olay için uygun olan ile öğesine yazılır:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-211">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="4c0cb-212">, `ParentComponent` Alt öğenin <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) metodunu kendi yöntemine ayarlar `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-212">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="4c0cb-213">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-213">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="4c0cb-214">Düğme ' de seçildiğinde `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="4c0cb-214">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="4c0cb-215">`ParentComponent`Öğesinin `ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-215">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="4c0cb-216">`messageText` güncelleştirilir ve içinde görüntülenir `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-216">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="4c0cb-217">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)Geri çağırma yönteminde () bir çağrısı gerekli değildir `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-217">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="4c0cb-218"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> alt `ParentComponent` Olaylar, alt öğe içinde yürütülen olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-218"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="4c0cb-219"><xref:Microsoft.AspNetCore.Components.EventCallback> ve <xref:Microsoft.AspNetCore.Components.EventCallback%601> zaman uyumsuz temsilcilere izin verir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-219"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="4c0cb-220"><xref:Microsoft.AspNetCore.Components.EventCallback> kesin olarak yazılmış ve içinde herhangi bir tür bağımsız değişkeni geçişine izin veriyor `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-220"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="4c0cb-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> kesin bir şekilde türdedir ve `T` Bu öğesine atanabilen bir bağımsız değişken geçirilmesini gerektirir `InvokeAsync(T)` `TValue` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="4c0cb-222">Bir <xref:Microsoft.AspNetCore.Components.EventCallback> veya <xref:Microsoft.AspNetCore.Components.EventCallback%601> ile çağırın <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ve şunu bekler <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="4c0cb-222">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="4c0cb-223"><xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> Olay işleme ve bağlama bileşeni parametreleri için ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-223">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="4c0cb-224">Kesin olarak belirlenmiş türü tercih edin <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-224">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="4c0cb-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="4c0cb-226">Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-226">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="4c0cb-227"><xref:Microsoft.AspNetCore.Components.EventCallback>Geri çağırmaya hiçbir değer geçirilmemişse kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-227">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="4c0cb-228">Varsayılan eylemleri engelle</span><span class="sxs-lookup"><span data-stu-id="4c0cb-228">Prevent default actions</span></span>

<span data-ttu-id="4c0cb-229">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-229">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="4c0cb-230">Giriş cihazında bir anahtar seçildiğinde ve öğe odağı bir metin kutusunda olduğunda, bir tarayıcı normalde metin kutusunda anahtarın karakterini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-230">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="4c0cb-231">Aşağıdaki örnekte, Directive özniteliği belirtilerek varsayılan davranış engellenir `@onkeypress:preventDefault` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-231">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="4c0cb-232">Sayaç artar ve **+** anahtar `<input>` öğenin değerine yakalanmaz:</span><span class="sxs-lookup"><span data-stu-id="4c0cb-232">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="4c0cb-233">`@on{EVENT}:preventDefault`Özniteliği bir değer olmadan belirtmek ile eşdeğerdir `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="4c0cb-233">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="4c0cb-234">Özniteliğin değeri de bir ifade olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-234">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="4c0cb-235">Aşağıdaki örnekte, `shouldPreventDefault` `bool` ya da olarak ayarlanan bir alandır `true` `false` :</span><span class="sxs-lookup"><span data-stu-id="4c0cb-235">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="4c0cb-236">Olay yaymayı durdur</span><span class="sxs-lookup"><span data-stu-id="4c0cb-236">Stop event propagation</span></span>

<span data-ttu-id="4c0cb-237">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Olay yaymayı durdurmak için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c0cb-237">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="4c0cb-238">Aşağıdaki örnekte, onay kutusunun seçilmesi ikinci alt öğeden üst öğeye yayılan olay tıklamasını önler `<div>` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="4c0cb-238">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
