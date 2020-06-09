---
title: ASP.NET Core 3,1 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 5b6ae8173ca3d968e220faa4a060e1b42b14f8bb
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507250"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="dcda5-103">ASP.NET Core 3,1 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="dcda5-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="dcda5-104">Bu makalede, ASP.NET Core 3,1 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="dcda5-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="dcda5-105">Bileşenler için kısmi sınıf desteği Razor</span><span class="sxs-lookup"><span data-stu-id="dcda5-105">Partial class support for Razor components</span></span>

Razor<span data-ttu-id="dcda5-106">bileşenler artık kısmi sınıflar olarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="dcda5-106"> components are now generated as partial classes.</span></span> <span data-ttu-id="dcda5-107">Bir bileşenin kodu, Razor tek bir dosyada bileşen için tüm kodu tanımlamak yerine kısmi bir sınıf olarak tanımlanmış bir arka plan kod dosyası kullanılarak yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="dcda5-108">Daha fazla bilgi için bkz. [kısmi sınıf desteği](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="dcda5-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="dcda5-109">Bileşen etiketi Yardımcısı ve parametreleri en üst düzey bileşenlere geçir</span><span class="sxs-lookup"><span data-stu-id="dcda5-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="dcda5-110">BlazorASP.NET Core 3,0 ' de, BILEŞENLER HTML Yardımcısı () kullanılarak sayfalar ve görünümler halinde işlenmiştir `Html.RenderComponentAsync` .</span><span class="sxs-lookup"><span data-stu-id="dcda5-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="dcda5-111">ASP.NET Core 3,1 ' de, yeni bileşen etiketi Yardımcısı ile bir sayfadan veya görünümden bir bileşeni işleme:</span><span class="sxs-lookup"><span data-stu-id="dcda5-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="dcda5-112">HTML Yardımcısı ASP.NET Core 3,1 ' de desteklenmeye devam eder, ancak bileşen etiketi Yardımcısı önerilir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="dcda5-113">Sunucu uygulamaları artık ilk işleme sırasında parametreleri en üst düzey bileşenlere geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="dcda5-114">Daha önce, parametreleri yalnızca [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)olan en üst düzey bileşene geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dcda5-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="dcda5-115">Bu sürümle birlikte, hem [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) hem de [RenderMode. Serverprerenimli](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) desteklenir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="dcda5-116">Belirtilen parametre değerleri JSON olarak serileştirilir ve başlangıç yanıtına dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="dcda5-117">Örneğin, `Counter` artış miktarı () olan bir bileşen için `IncrementAmount` :</span><span class="sxs-lookup"><span data-stu-id="dcda5-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="dcda5-118">Daha fazla bilgi için bkz. [bileşenleri Razor Sayfalar ve MVC uygulamaları ile tümleştirme](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="dcda5-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="dcda5-119">HTTP. sys dosyasındaki paylaşılan sıralar için destek</span><span class="sxs-lookup"><span data-stu-id="dcda5-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="dcda5-120">[Http. sys](xref:fundamentals/servers/httpsys) , anonim istek kuyrukları oluşturmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="dcda5-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="dcda5-121">ASP.NET Core 3,1 ' de, var olan bir HTTP. sys istek kuyruğunu oluşturma veya ekleme yeteneğine ekledik.</span><span class="sxs-lookup"><span data-stu-id="dcda5-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="dcda5-122">Var olan adlandırılmış bir HTTP. sys istek kuyruğunu oluşturma veya iliştirme, sıranın sahibi olan HTTP. sys denetleyici işleminin dinleyici işleminden bağımsız olduğu senaryolara olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="dcda5-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="dcda5-123">Bu bağımsızlık, var olan bağlantıları ve dinleyici işlemi yeniden başlatmalar arasında sıraya alınmış istekleri korumayı mümkün kılar:</span><span class="sxs-lookup"><span data-stu-id="dcda5-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="dcda5-124">SameSite tanımlama bilgilerine yönelik son değişiklikler</span><span class="sxs-lookup"><span data-stu-id="dcda5-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="dcda5-125">SameSite tanımlama bilgilerinin davranışı yaklaşan tarayıcı değişikliklerini yansıtacak şekilde değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="dcda5-126">Bu, AzureAd, Openıdconnect veya WsFederation gibi kimlik doğrulama senaryolarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="dcda5-127">Daha fazla bilgi için bkz. <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="dcda5-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a><span data-ttu-id="dcda5-128">Uygulamalardaki olaylar için varsayılan eylemleri engelleme Blazor</span><span class="sxs-lookup"><span data-stu-id="dcda5-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="dcda5-129">`@on{EVENT}:preventDefault`Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="dcda5-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="dcda5-130">Aşağıdaki örnekte, metin kutusunda anahtarın karakterini görüntülemenin varsayılan eylemi engellenir:</span><span class="sxs-lookup"><span data-stu-id="dcda5-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="dcda5-131">Daha fazla bilgi için bkz. [varsayılan eylemleri engelleme](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="dcda5-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-blazor-apps"></a><span data-ttu-id="dcda5-132">Uygulamalarda olay yaymayı Durdur Blazor</span><span class="sxs-lookup"><span data-stu-id="dcda5-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="dcda5-133">`@on{EVENT}:stopPropagation`Olay yaymayı durdurmak için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="dcda5-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="dcda5-134">Aşağıdaki örnekte, onay kutusunun seçilmesi alt öğeden `<div>` üst öğeye yayılmalarını önler `<div>` :</span><span class="sxs-lookup"><span data-stu-id="dcda5-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="dcda5-135">Daha fazla bilgi için bkz. [olay yaymayı durdurma](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="dcda5-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-blazor-app-development"></a><span data-ttu-id="dcda5-136">Uygulama geliştirme sırasında ayrıntılı hatalar Blazor</span><span class="sxs-lookup"><span data-stu-id="dcda5-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="dcda5-137">BlazorGeliştirme sırasında bir uygulama düzgün çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerini alma sorunu gidermeye ve soruna yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="dcda5-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="dcda5-138">Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:</span><span class="sxs-lookup"><span data-stu-id="dcda5-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="dcda5-139">Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="dcda5-140">Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.</span><span class="sxs-lookup"><span data-stu-id="dcda5-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="dcda5-141">Daha fazla bilgi için bkz. [geliştirme sırasında ayrıntılı hatalar](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="dcda5-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
