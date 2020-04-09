---
title: ASP.NET Core 3.1'deki yenilikler
author: rick-anderson
description: ASP.NET Core 3.1'deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662710"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="dbf27-103">ASP.NET Core 3.1'deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="dbf27-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="dbf27-104">Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 3.1 en önemli değişiklikleri vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="dbf27-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="dbf27-105">Razor bileşenleri için kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="dbf27-105">Partial class support for Razor components</span></span>

<span data-ttu-id="dbf27-106">Jilet bileşenleri artık kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dbf27-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="dbf27-107">Bir Razor bileşeninin kodu, tek bir dosyadaki bileşenin tüm kodunu tanımlamak yerine kısmi sınıf olarak tanımlanan kod arkası dosyası kullanılarak yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="dbf27-108">Daha fazla bilgi için [Kısmi sınıf desteğine](xref:blazor/components#partial-class-support)bakın.</span><span class="sxs-lookup"><span data-stu-id="dbf27-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="dbf27-109">Bileşen Tag Helper ve parametreleri üst düzey bileşenlere geçirin</span><span class="sxs-lookup"><span data-stu-id="dbf27-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="dbf27-110">Core Blazor 3.0 ASP.NET ile bileşenler, html yardımcısı kullanılarak sayfalara`Html.RenderComponentAsync`ve görünümlere işlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="dbf27-111">Core 3.1ASP.NET, yeni Bileşen Etiket Yardımcısı ile bir sayfadan veya görünümden bir bileşen oluşturma:</span><span class="sxs-lookup"><span data-stu-id="dbf27-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="dbf27-112">HTML Yardımcısı ASP.NET Core 3.1'de desteklenmeye devam eder, ancak Bileşen Etiket Yardımcısı önerilir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="dbf27-113">Sunucu uygulamaları artık parametreleri ilk işleme sırasında üst düzey bileşenlere aktarabilir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="dbf27-114">Daha önce yalnızca [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)ile bir üst düzey bileşene parametreleri geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dbf27-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="dbf27-115">Bu sürümle hem [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) hem de [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) desteklenir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="dbf27-116">Belirtilen parametre değerleri JSON olarak serihale edilir ve ilk yanıta dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="dbf27-117">Örneğin, bir `Counter` bileşeni artış tutarıyla önceden`IncrementAmount`işlemek ( ):</span><span class="sxs-lookup"><span data-stu-id="dbf27-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="dbf27-118">Daha fazla bilgi için [bkz.](xref:blazor/integrate-components)</span><span class="sxs-lookup"><span data-stu-id="dbf27-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="dbf27-119">HTTP.sys'deki paylaşılan kuyruklar için destek</span><span class="sxs-lookup"><span data-stu-id="dbf27-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="dbf27-120">[HTTP.sys](xref:fundamentals/servers/httpsys) anonim istek kuyrukları oluşturmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="dbf27-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="dbf27-121">Core 3.1ASP.NET, mevcut bir HTTP.sys istek kuyruğu oluşturma veya ekleme özelliğine ekledik.</span><span class="sxs-lookup"><span data-stu-id="dbf27-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="dbf27-122">Varolan bir HTTP.sys istek kuyruğu oluşturma veya iliştirme, sıranın sahibi http.sys denetleyicisi işleminin dinleyici işleminden bağımsız olduğu senaryolara olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="dbf27-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="dbf27-123">Bu bağımsızlık, varolan bağlantıların ve dinleyici işlemi yeniden başlatmaları arasında sıralanmış isteklerin korunmasını mümkün kılar:</span><span class="sxs-lookup"><span data-stu-id="dbf27-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="dbf27-124">SameSite çerezleri için son dakika değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="dbf27-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="dbf27-125">SameSite tanımlama bilgilerinin davranışı, yaklaşan tarayıcı değişikliklerini yansıtacak şekilde değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="dbf27-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="dbf27-126">Bu, AzureAd, OpenIdConnect veya WsFederation gibi kimlik doğrulama senaryolarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="dbf27-127">Daha fazla bilgi için bkz. <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="dbf27-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="dbf27-128">Uygulamalardaki Blazor olaylar için varsayılan eylemleri önleme</span><span class="sxs-lookup"><span data-stu-id="dbf27-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="dbf27-129">Bir `@on{EVENT}:preventDefault` olay için varsayılan eylemi önlemek için yönerge özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="dbf27-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="dbf27-130">Aşağıdaki örnekte, metin kutusunda anahtarın karakterini görüntülemenin varsayılan eylemi engellenir:</span><span class="sxs-lookup"><span data-stu-id="dbf27-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="dbf27-131">Daha fazla bilgi için [bkz.](xref:blazor/event-handling#prevent-default-actions)</span><span class="sxs-lookup"><span data-stu-id="dbf27-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="dbf27-132">Uygulamalarda Blazor olay yayılmasını durdurma</span><span class="sxs-lookup"><span data-stu-id="dbf27-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="dbf27-133">Olay `@on{EVENT}:stopPropagation` yayılmasını durdurmak için yönerge özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="dbf27-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="dbf27-134">Aşağıdaki örnekte, onay kutusunu n seçilmesi, alt `<div>` öğenin üst öğeye `<div>`yayılmasını engelleyen tıklama olaylarıdır:</span><span class="sxs-lookup"><span data-stu-id="dbf27-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="dbf27-135">Daha fazla bilgi için olay [yayılmasını durdur](xref:blazor/event-handling#stop-event-propagation)una bakın.</span><span class="sxs-lookup"><span data-stu-id="dbf27-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="dbf27-136">Uygulama geliştirme Blazor sırasında ayrıntılı hatalar</span><span class="sxs-lookup"><span data-stu-id="dbf27-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="dbf27-137">Bir Blazor uygulama geliştirme sırasında düzgün çalışmadığında, uygulamadan ayrıntılı hata bilgileri almak sorun giderme ve sorunu gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="dbf27-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="dbf27-138">Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında bir altın çubuk görüntüler:</span><span class="sxs-lookup"><span data-stu-id="dbf27-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="dbf27-139">Geliştirme sırasında, gold bar sizi özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="dbf27-140">Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirer ve tarayıcının yenilenmesini önerir.</span><span class="sxs-lookup"><span data-stu-id="dbf27-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="dbf27-141">Daha fazla bilgi için [geliştirme sırasında ayrıntılı hatalar](xref:blazor/handle-errors#detailed-errors-during-development)abakın.</span><span class="sxs-lookup"><span data-stu-id="dbf27-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
