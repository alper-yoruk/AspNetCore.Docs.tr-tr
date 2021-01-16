---
title: ASP.NET Core Blazor bileşen işleme
author: guardrex
description: RazorASP.NET Core uygulamalarda bileşen işleme hakkında bilgi edinin Blazor ve StateHasChanged çağrısının ne zaman çağrılacağını de dahil edin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
no-loc:
- appsettings.json
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
uid: blazor/components/rendering
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253976"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a><span data-ttu-id="748ff-103">ASP.NET Core Blazor bileşen işleme</span><span class="sxs-lookup"><span data-stu-id="748ff-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="748ff-104">[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="748ff-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="748ff-105">Bileşenler, ana bileşenleri tarafından bileşen hiyerarşisine İlk eklendiğinde *işlenmelidir* .</span><span class="sxs-lookup"><span data-stu-id="748ff-105">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="748ff-106">Bu, bir bileşenin tamamen işlenmesi gereken tek zaman olur.</span><span class="sxs-lookup"><span data-stu-id="748ff-106">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="748ff-107">Bileşenler kendi mantığına ve kurallarına göre herhangi bir zamanda işlemeyi tercih *edebilir* .</span><span class="sxs-lookup"><span data-stu-id="748ff-107">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="748ff-108">İçin kurallar `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="748ff-108">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="748ff-109">Varsayılan olarak, Razor Components ( `.razor` ), <xref:Microsoft.AspNetCore.Components.ComponentBase> aşağıdaki saatlerde rerendering tetikleme mantığını içeren temel sınıftan devralınır:</span><span class="sxs-lookup"><span data-stu-id="748ff-109">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="748ff-110">Bir üst bileşenden güncelleştirilmiş bir parametre kümesi uyguladıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="748ff-110">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="748ff-111">Basamaklı bir parametre için güncelleştirilmiş bir değer uygulandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="748ff-111">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="748ff-112">Bir olayın bildiriminde ve kendi olay işleyicilerinden birini çağırarak.</span><span class="sxs-lookup"><span data-stu-id="748ff-112">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="748ff-113">Kendi yöntemine yapılan çağrıdan sonra <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="748ff-113">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="748ff-114"><xref:Microsoft.AspNetCore.Components.ComponentBase>Aşağıdakilerden biri doğru olursa, parametre güncelleştirmeleri nedeniyle bu öğeden devralan bileşenler atlayın:</span><span class="sxs-lookup"><span data-stu-id="748ff-114">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="748ff-115">Tüm parametre değerleri, bilinen sabit ilkel türlerdir (örneğin,,, `int` `string` `DateTime` ) ve önceki parametre kümesi ayarlanmasından bu yana değiştirilmez.</span><span class="sxs-lookup"><span data-stu-id="748ff-115">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="748ff-116">Bileşenin <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> yöntemi döndürür `false` .</span><span class="sxs-lookup"><span data-stu-id="748ff-116">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="748ff-117">Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> bkz <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender> ..</span><span class="sxs-lookup"><span data-stu-id="748ff-117">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="748ff-118">İşleme akışını denetleme</span><span class="sxs-lookup"><span data-stu-id="748ff-118">Control the rendering flow</span></span>

<span data-ttu-id="748ff-119">Çoğu durumda, <xref:Microsoft.AspNetCore.Components.ComponentBase> kurallar bir olay oluştuktan sonra doğru bileşen alt kümesiyle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="748ff-119">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="748ff-120">Geliştiriciler, çerçeveye hangi bileşenlerin yeniden başlatılacağını ve ne zaman yeniden yeniden örnekleyeceğinizi belirten el ile mantık sağlamak için genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="748ff-120">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="748ff-121">Çerçevenin kurallarının genel etkisi, bir olayı alan bileşenin kendisini tekrar tekrar harekete geçirmesidir ve bu da parametre değerleri değişmiş olabilecek alt bileşenlerin rerendering yinelemeli olarak tetikler.</span><span class="sxs-lookup"><span data-stu-id="748ff-121">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="748ff-122">Çerçevenin kurallarının performans etkileri ve bir uygulamanın bileşen hiyerarşisinin nasıl iyileştirileceği hakkında daha fazla bilgi için bkz <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> ..</span><span class="sxs-lookup"><span data-stu-id="748ff-122">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="748ff-123">Ne zaman çağrılacağını `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="748ff-123">When to call `StateHasChanged`</span></span>

<span data-ttu-id="748ff-124"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>Yöntemi, bir işlemeyi istediğiniz zaman tetiklemeniz için izin verir.</span><span class="sxs-lookup"><span data-stu-id="748ff-124">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="748ff-125">Ancak, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> gereksiz işleme maliyetlerini sağladığından, yaygın bir hata olan gereksiz bir hata çağırmamaya dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="748ff-125">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="748ff-126">*Şu* durumlarda çağırmanız gerekmez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="748ff-126">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="748ff-127"><xref:Microsoft.AspNetCore.Components.ComponentBase>Birçok rutin olay işleyicisi için bir işlemeyi tetiklediği zaman uyumlu veya zaman uyumsuz olarak olayları düzenli olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="748ff-127">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="748ff-128">Genel yaşam [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) <xref:Microsoft.AspNetCore.Components.ComponentBase> döngüsü olayları için bir işlemeyi tetikleyip, veya gibi tipik yaşam döngüsü mantığını uygulama.</span><span class="sxs-lookup"><span data-stu-id="748ff-128">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="748ff-129">Ancak, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Aşağıdaki bölümlerde açıklanan durumlarda çağrı yapmak mantıklı olabilir:</span><span class="sxs-lookup"><span data-stu-id="748ff-129">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="748ff-130">Zaman uyumsuz bir işleyici birden çok zaman uyumsuz aşama içerir</span><span class="sxs-lookup"><span data-stu-id="748ff-130">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="748ff-131">Blazorİşleme ve olay işleme sistemine bir dış öğe çağrısı alma</span><span class="sxs-lookup"><span data-stu-id="748ff-131">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="748ff-132">Bileşeni belirli bir olay tarafından yeniden kullanılan alt ağacın dışında işlemek için</span><span class="sxs-lookup"><span data-stu-id="748ff-132">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="748ff-133">Zaman uyumsuz bir işleyici birden çok zaman uyumsuz aşama içerir</span><span class="sxs-lookup"><span data-stu-id="748ff-133">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="748ff-134">`Counter`Her tıklama için sayıyı dört kez güncelleştiren aşağıdaki bileşeni göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="748ff-134">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="748ff-135">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="748ff-135">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="748ff-136">Görevlerin .NET 'te tanımlanmasıyla ilgili olarak bir alıcısı, <xref:System.Threading.Tasks.Task> Ara zaman uyumsuz durumlar yerine yalnızca son tamamlanmasını gözlemlemez.</span><span class="sxs-lookup"><span data-stu-id="748ff-136">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="748ff-137">Bu nedenle, <xref:Microsoft.AspNetCore.Components.ComponentBase> yalnızca <xref:System.Threading.Tasks.Task> ilk kez döndürüldüğünde ve son tamamlandığında rerendering tetiklenebilir <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="748ff-137">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="748ff-138">Diğer ara noktalarda yeniden günlüğe eklemek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="748ff-138">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="748ff-139">Ara noktalarda yeniden kullanmak istiyorsanız kullanın <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="748ff-139">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a><span data-ttu-id="748ff-140">Blazorİşleme ve olay işleme sistemine bir dış öğe çağrısı alma</span><span class="sxs-lookup"><span data-stu-id="748ff-140">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="748ff-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> yalnızca kendi yaşam döngüsü yöntemleri ve tetiklenmiş olayları hakkında bilgi sahibi Blazor .</span><span class="sxs-lookup"><span data-stu-id="748ff-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="748ff-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> kodunuzda oluşabilecek diğer olaylar hakkında bilgi sahibi değildir.</span><span class="sxs-lookup"><span data-stu-id="748ff-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="748ff-143">Örneğin, özel bir veri deposu tarafından oluşturulan tüm C# olayları için olarak bilinmez Blazor .</span><span class="sxs-lookup"><span data-stu-id="748ff-143">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="748ff-144">Bu tür olayların Kullanıcı arabirimindeki güncelleştirilmiş değerleri görüntülemesi için rerendering tetiklenmesi için kullanın <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="748ff-144">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="748ff-145">Başka bir kullanım durumunda, `Counter` sayıyı düzenli bir aralıkta güncelleştirmek için tarafından kullanılan aşağıdaki bileşeni göz önünde bulundurun <xref:System.Timers.Timer?displayProperty=fullName> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Kullanıcı arabirimini güncelleştirmek için çağırır.</span><span class="sxs-lookup"><span data-stu-id="748ff-145">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="748ff-146">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="748ff-146">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="748ff-147">Önceki örnekte, `OnTimerCallback` çağrı <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Blazor içindeki değişiklikleri farkında olmadığından çağrılmalıdır `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="748ff-147">In the preceding example, `OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="748ff-148">`OnTimerCallback` , Blazor yönetilen işleme akışı veya olay bildiriminin dışında çalışır.</span><span class="sxs-lookup"><span data-stu-id="748ff-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>

<span data-ttu-id="748ff-149">Benzer şekilde, geri çağırma, Blazor eşitleme bağlamı dışında çağrıldığı için, içindeki mantığı, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> oluşturucunun eşitleme bağlamına taşımak üzere kaydırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="748ff-149">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="748ff-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> yalnızca oluşturucunun eşitleme bağlamından çağrılabilir, aksi takdirde bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="748ff-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="748ff-151">Bu, diğer kullanıcı arabirimi çerçevelerinden UI iş parçacığına sıralama ile eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="748ff-151">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="748ff-152">Bir bileşeni belirli bir olay tarafından yeniden eklenen alt ağacın dışında işlemek için</span><span class="sxs-lookup"><span data-stu-id="748ff-152">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="748ff-153">Kullanıcı arabiriminiz bir bileşene bir olay gönderme, bazı durumları değiştirme ve olayı alan bir alt öğesi olmayan tamamen farklı bir bileşeni yeniden oluşturma gereksinimi içerebilir.</span><span class="sxs-lookup"><span data-stu-id="748ff-153">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="748ff-154">Bu senaryoyu ele almanın bir yolu, büyük olasılıkla bir DI hizmeti olarak birden çok bileşene eklenen bir *durum yönetimi* sınıfına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="748ff-154">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="748ff-155">Bir bileşen durum yöneticisinde bir yöntemi çağırdığında, durum Yöneticisi bir bağımsız bileşen tarafından alınan bir C# olayını tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="748ff-155">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="748ff-156">Bu C# olayları, işleme işlem hattının dışında olduğundan Blazor , <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> durum yöneticisinin olaylarına yanıt olarak işlemek istediğiniz diğer bileşenlere çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="748ff-156">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="748ff-157">Bu, <xref:System.Timers.Timer?displayProperty=fullName> [önceki bölüm](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) bölümünde daha önceki bir durum ile benzerdir.</span><span class="sxs-lookup"><span data-stu-id="748ff-157">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="748ff-158">Yürütme çağrı yığını genellikle oluşturucunun eşitleme bağlamında kaldığından, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> normalde gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="748ff-158">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="748ff-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> yalnızca mantığın, ' a çağrı yapma <xref:System.Threading.Tasks.Task.ContinueWith%2A> <xref:System.Threading.Tasks.Task> ya da bir ile bekleme gibi eşitleme bağlamını iptal eden olması durumunda <xref:System.Threading.Tasks.Task> gereklidir [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .</span><span class="sxs-lookup"><span data-stu-id="748ff-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
