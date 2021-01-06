---
title: ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri
author: pranavkm
description: ASP.NET Core uygulamalarında performansı artırma Blazor WebAssembly ve sık karşılaşılan performans sorunlarından kaçınmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: cc090b4e56745e6b010e4a7ee17332b0d3a95560
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417389"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="ccd27-103">ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="ccd27-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="ccd27-104">, [Pranav Krishnamoorthy](https://github.com/pranavkm) ve [Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="ccd27-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="ccd27-105">Blazor WebAssembly , en gerçekçi uygulama kullanıcı arabirimi senaryolarında yüksek performansa olanak tanımak için dikkatle tasarlanır ve iyileştirilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="ccd27-106">Ancak en iyi sonuçları üretmek, doğru desenleri ve özellikleri kullanan geliştiricilere bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="ccd27-107">Aşağıdaki noktaları göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ccd27-107">Consider the following aspects:</span></span>

* <span data-ttu-id="ccd27-108">**Çalışma zamanı işleme**: .NET kodu WebAssembly çalışma zamanının içindeki yorumlayıcı üzerinde çalışır, bu nedenle CPU üretilen işi sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="ccd27-109">Yoğun senaryolarda uygulama, [işleme hızını iyileştirmenin](#optimize-rendering-speed)avantajlarından yararlanır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="ccd27-110">**Başlangıç zamanı**: uygulama bir .NET çalışma zamanını tarayıcıya aktarır, bu nedenle [uygulama indirme boyutunu en aza indirecek](#minimize-app-download-size)Özellikler kullanılması önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="ccd27-111">İşleme hızını iyileştirme</span><span class="sxs-lookup"><span data-stu-id="ccd27-111">Optimize rendering speed</span></span>

<span data-ttu-id="ccd27-112">Aşağıdaki bölümler, işleme iş yükünü en aza indirmek ve UI yanıt hızını artırmak için öneriler sağlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="ccd27-113">Bu tavsiyeler, UI işleme hızlarındaki *on katlamalı veya daha yüksek bir geliştirmeyi* kolayca yapabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="ccd27-114">Bileşen alt ağaçlarını gereksiz şekilde işlemesini önleyin</span><span class="sxs-lookup"><span data-stu-id="ccd27-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="ccd27-115">Çalışma zamanında, bileşenler hiyerarşi olarak mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="ccd27-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="ccd27-116">Kök bileşenin alt bileşenleri vardır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-116">A root component has child components.</span></span> <span data-ttu-id="ccd27-117">Sırasıyla, kök alt bileşenleri kendi alt bileşenlerine sahiptir ve bu şekilde devam eder.</span><span class="sxs-lookup"><span data-stu-id="ccd27-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="ccd27-118">Kullanıcı bir düğme seçen bir olay gerçekleştiğinde, bu, Blazor Hangi bileşenlere yeniden değer vermek için bu şekilde çalışır:</span><span class="sxs-lookup"><span data-stu-id="ccd27-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

 1. <span data-ttu-id="ccd27-119">Olayın kendisi, olayın işleyicisini işlenmiş olan bileşene gönderilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="ccd27-120">Olay işleyicisini yürüttükten sonra, bu bileşen yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="ccd27-121">Herhangi bir bileşen yeniden her eklendiğinde, her bir alt bileşeninin parametre değerlerinin yeni bir kopyasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="ccd27-122">Yeni bir parametre değerleri kümesi alınırken, her bileşen yeniden tekrar olup olmayacağını seçer.</span><span class="sxs-lookup"><span data-stu-id="ccd27-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="ccd27-123">Varsayılan olarak, parametre değerleri değişmiş olabilir (örneğin, değişebilir nesnelerse), bileşenler yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="ccd27-124">Bu dizinin son iki adımı, bileşen hiyerarşisinde yinelemeli olarak devam eder.</span><span class="sxs-lookup"><span data-stu-id="ccd27-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="ccd27-125">Çoğu durumda, tüm alt ağaç yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="ccd27-126">Bu, üst düzey bileşenleri hedefleyen olayların pahalı rerendering işlemlerine neden olabileceği anlamına gelir, çünkü bu noktanın altındaki her şey yeniden alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="ccd27-127">Bu işlemi kesintiye uğratmak ve belirli bir alt ağaçta işleme özyinelemeyi engellemek istiyorsanız şunlardan birini yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ccd27-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="ccd27-128">Belirli bir bileşene yönelik tüm parametrelerin temel sabit türlerde (örneğin,,,, `string` `int` `bool` `DateTime` ve diğerleri) olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="ccd27-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="ccd27-129">Bu parametre değerlerinden hiçbiri değişmediği takdirde değişiklikleri tespit etmek için yerleşik mantık rerendering öğesini otomatik olarak atlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="ccd27-130">Bir alt bileşeni ile oluşturduysanız `<Customer CustomerId="@item.CustomerId" />` , burada `CustomerId` bir değer olduğunda, `int` değişiklik ne zaman haricinde yeniden verilmez `item.CustomerId` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="ccd27-131">Özel model türleri, olay geri çağırmaları veya değerler gibi temel olmayan parametre değerlerini kabul etmeniz gerekirse, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> oluşturma bölümünde açıklanan [ `ShouldRender` ](#use-of-shouldrender) kararı denetlemek için geçersiz kılabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="ccd27-132">Tüm alt ağaçlar rerendering atlanarak, bir olay gerçekleştiğinde işleme maliyetinin büyük çoğunluğunu kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="ccd27-133">UI 'nin bir parçası olan rerendering atlayabilmeniz için, özellikle alt bileşenleri çarpanlara ayırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="ccd27-134">Bu, bir üst bileşenin işleme maliyetini azaltmanın geçerli bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="ccd27-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="ccd27-135">Kullanım `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="ccd27-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="ccd27-136">Tek bir kullanıcı arabirimi yazıyorsanız (herhangi bir parametre değerinden bağımsız olarak) ilk işleme sonrasında hiçbir şekilde değişmez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="ccd27-137">Bileşen yalnızca parametre değerleri belirli yöntemlerle rerendering gerektiriyorsa, değişiklikleri algılamak üzere gerekli bilgileri izlemek için özel alanları kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="ccd27-138">Aşağıdaki örnekte, yeniden `shouldRender` oluşturması gereken her türlü değişiklik veya mutasyon için denetimi temel alır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="ccd27-139">`prevOutboundFlightId` ve `prevInboundFlightId` sonraki olası güncelleştirme için bilgileri izleyin:</span><span class="sxs-lookup"><span data-stu-id="ccd27-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="ccd27-140">Önceki kodda, bir olay işleyicisi, `shouldRender` `true` bileşenin olaydan sonra yeniden bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde olarak da</span><span class="sxs-lookup"><span data-stu-id="ccd27-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="ccd27-141">Çoğu bileşen için bu düzeyde el ile denetim gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="ccd27-142">Yalnızca söz konusu alt ağaçlar işlemeye pahalı ise ve UI gecikmeye neden oluyorsa işleme alt ağaçlarını atlama konusunda endişelenmelisiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="ccd27-143">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="ccd27-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="ccd27-144">Sanallaştırma</span><span class="sxs-lookup"><span data-stu-id="ccd27-144">Virtualization</span></span>

<span data-ttu-id="ccd27-145">Bir döngüde büyük miktarlarda Kullanıcı arabirimini işlerken (örneğin, binlerce girişi olan bir liste veya kılavuz), işleme işlemlerinin birlikte işlenmesi, kullanıcı ARABIRIMI oluşturma işlemlerindeki bir gecikme süresine neden olabilir ve bu nedenle kötü bir kullanıcı deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="ccd27-146">Kullanıcının aynı anda yalnızca küçük sayıda öğeyi kaydırma yapmadan görebildiğinden, bu durumda, o anda görünür olmayan öğeleri çok fazla işlemek çok zaman harcayabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="ccd27-147">Bunu çözmek için, rastgele Blazor büyük bir listenin Görünüm ve kaydırma davranışlarını oluşturan, ancak gerçekte yalnızca geçerli kaydırma görünüm penceresinin içindeki liste öğelerini işleyen yerleşik bir [ `<Virtualize>` bileşen](xref:blazor/components/virtualization) sağlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-147">To address this, Blazor provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="ccd27-148">Örneğin, bu, uygulamanın 100.000 girişi olan bir listesi olabileceği, ancak yalnızca herhangi bir zamanda görünür olan 20 öğenin işleme maliyetini ödeme anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="ccd27-149">Bileşenin kullanımı, `<Virtualize>` Kullanıcı arabirimi performansını büyüklük siparişlerine göre ölçeklendirebilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="ccd27-150">`<Virtualize>` Şu durumlarda kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="ccd27-151">Bir döngüde veri öğeleri kümesi işleme.</span><span class="sxs-lookup"><span data-stu-id="ccd27-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="ccd27-152">Öğelerin çoğu kaydırma nedeniyle görünür değil.</span><span class="sxs-lookup"><span data-stu-id="ccd27-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="ccd27-153">İşlenen öğeler tam olarak aynı boyutta.</span><span class="sxs-lookup"><span data-stu-id="ccd27-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="ccd27-154">Kullanıcı rastgele bir noktaya kaydığında, bileşen gösterilecek görünür öğeleri hesaplayabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="ccd27-155">Aşağıda, sanallaştırılmış olmayan bir liste örneği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="ccd27-156">`allFlights`Koleksiyonda 10.000 öğe varsa, bu, 10.000 bileşen örneklerini oluşturur ve işler `<FlightSummary>` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="ccd27-157">Buna karşılık aşağıda, sanallaştırılmış bir liste örneği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="ccd27-158">Sonuçta elde edilen kullanıcı ARABIRIMI bir kullanıcıya aynı olsa da, arka planda bileşen yalnızca başlatılır ve `<FlightSummary>` kaydırılabilir bölgeyi dolduracak kadar birçok örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ccd27-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="ccd27-159">`<FlightSummary>`Görünen örnek kümesi, Kullanıcı kaydırıldığında yeniden hesaplanır ve işlenir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="ccd27-160">`<Virtualize>` diğer avantajları da vardır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="ccd27-161">Örneğin, bir bileşen bir dış API 'den veri istediğinde, `<Virtualize>` bileşenin tüm verileri koleksiyondan indirmek yerine yalnızca geçerli görünür bölgeye karşılık gelen kayıt dilimini getirme izni verir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="ccd27-162">Daha fazla bilgi için bkz. <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="ccd27-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="ccd27-163">Hafif, iyileştirilmiş bileşenler oluşturma</span><span class="sxs-lookup"><span data-stu-id="ccd27-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="ccd27-164">Çoğu Blazor bileşen agresif iyileştirme çabalarına gerek kalmaz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-164">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="ccd27-165">Bunun nedeni çoğu bileşenin kullanıcı arabiriminde genellikle tekrarlanmamasından ve yüksek sıklıkta yeniden oturum etmamamasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="ccd27-166">Örneğin, `@page` iletişim kutuları veya formlar gibi üst düzey kullanıcı arabirimi parçalarını temsil eden bileşenler ve bileşenler, büyük olasılıkla yalnızca bir kez ve Kullanıcı hareketiyle yanıt olarak yalnızca bir kez görünür.</span><span class="sxs-lookup"><span data-stu-id="ccd27-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="ccd27-167">Bu bileşenler yüksek bir işleme iş yükü oluşturmaz, bu sayede, işleme performansı hakkında endişelenmeden istediğiniz Framework özelliklerinin herhangi bir birleşimini ücretsiz olarak kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="ccd27-168">Ancak, ölçeklendirilmesi gereken bileşenleri oluşturduğunuz yaygın senaryolar da vardır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="ccd27-169">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ccd27-169">For example:</span></span>

 * <span data-ttu-id="ccd27-170">Büyük iç içe yerleştirilmiş formlarda yüzlerce ayrı giriş, etiket ve diğer öğeler bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="ccd27-171">Kılavuzlarda binlerce hücre olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="ccd27-172">Dağılım çizimleri milyonlarca veri noktasına sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="ccd27-173">Her birimi ayrı bileşen örnekleri olarak modelliyorsa, bunların çoğu, işleme performansının kritik hale gelebilmesini sağlayacak.</span><span class="sxs-lookup"><span data-stu-id="ccd27-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="ccd27-174">Bu bölümde, Kullanıcı arabiriminin hızlı ve hızlı bir şekilde kalması için bu tür bileşenleri basit hale getirme önerileri sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="ccd27-175">Binlerce bileşen örneği kullanmaktan kaçının</span><span class="sxs-lookup"><span data-stu-id="ccd27-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="ccd27-176">Her bileşen, üst ve alt öğelerinden bağımsız olarak işleyebilen ayrı bir adadır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="ccd27-177">Kullanıcı arabiriminin bir bileşen hiyerarşisine nasıl bölüneceği seçerek, Kullanıcı arabirimi işleme ayrıntı düzeyi üzerinde denetim elde edersiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="ccd27-178">Bu, performans için iyi veya hatalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="ccd27-179">Kullanıcı arabirimini daha fazla bileşene bölerek, olaylar gerçekleştiğinde UI 'nin daha küçük bir bölümünü kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="ccd27-180">Örneğin, bir Kullanıcı bir tablo satırındaki bir düğmeye tıkladığında, tüm sayfa veya tablo yerine yalnızca bu tek satıra yeniden oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="ccd27-181">Ancak, her ek bileşen bağımsız durum ve işleme yaşam döngüsü ile başa çıkmak için bazı ek bellek ve CPU ek yükü içerir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="ccd27-182">Blazor WebAssembly.NET 5 ' te performansını ayarlamamız, bileşen örneği başına 0,06 MS 'nin bir işleme ek yükünü ölçyoruz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-182">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="ccd27-183">Bu, tipik bir dizüstü bilgisayarda çalışan üç parametre kabul eden basit bir bileşeni temel alır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="ccd27-184">Dahili olarak, ek yükün büyük ölçüde sözlüklerden bileşen başına durumunun alınması ve parametrelerin geçirilmesi ve alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="ccd27-185">Çarpma ile, 2.000 ek bileşen örneği eklemenin işleme zamanına 0,12 saniye ekleymesinin ve Kullanıcı arabiriminin kullanıcılara ağır bir şekilde yavaşladığı hakkında bilgi alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="ccd27-186">Daha fazlasına sahip olabilmeleri için bileşenleri daha hafif hale getirmek mümkündür, ancak genellikle daha güçlü bir teknik çok sayıda bileşene sahip olmaz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="ccd27-187">Aşağıdaki bölümlerde iki yaklaşım açıklanır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="ccd27-188">Satır içi alt bileşenleri üst öğeleri</span><span class="sxs-lookup"><span data-stu-id="ccd27-188">Inline child components into their parents</span></span>

<span data-ttu-id="ccd27-189">Alt bileşenlerden oluşan bir diziyi işleyen aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ccd27-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="ccd27-190">Yukarıdaki örnek kod için bileşen, şunu `<ChatMessageDisplay>` içeren bir dosyada tanımlanmıştır `ChatMessageDisplay.razor` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="ccd27-191">Yukarıdaki örnek iyi çalışır ve binlerce ileti bir kerede gösterilmemektedir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="ccd27-192">Tek seferde binlerce iletiyi göstermek için ayrı bileşeni düzenleme *etmeyi* göz önünde bulundurun `ChatMessageDisplay` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="ccd27-193">Bunun yerine, doğrudan üst öğe içinde işleme satır içi:</span><span class="sxs-lookup"><span data-stu-id="ccd27-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="ccd27-194">Bu, çok sayıda alt bileşeni, her birinin bağımsız olarak yeniden yönlendirilmemesini sağlayacak şekilde işleme için bileşen başına ek yükünü önler.</span><span class="sxs-lookup"><span data-stu-id="ccd27-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="ccd27-195">Kodda yeniden kullanılabilir `RenderFragments` olarak tanımlayın</span><span class="sxs-lookup"><span data-stu-id="ccd27-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="ccd27-196">Yalnızca işleme mantığını yeniden kullanma gibi alt bileşenler düzenleme olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="ccd27-197">Bu durumda, gerçek bileşenleri bildirmeden işleme mantığını yeniden kullanmak yine de mümkündür.</span><span class="sxs-lookup"><span data-stu-id="ccd27-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="ccd27-198">Herhangi `@code` bir bileşenin bloğunda, <xref:Microsoft.AspNetCore.Components.RenderFragment> Kullanıcı arabirimini yayar ve her yerden çağrılabilecek bir ' i tanımlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ccd27-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="ccd27-199">Yukarıdaki örnekte belirtildiği gibi, bileşenler bloğunun içindeki ve dışındaki bir kodu işaret edebilir `@code` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="ccd27-200">Bu yaklaşım <xref:Microsoft.AspNetCore.Components.RenderFragment> , isteğe bağlı olarak birden çok yerde, bileşenin normal işleme çıkışı içinde işleyebilmeniz gereken bir temsilciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="ccd27-201">`__builder` <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> Razor Derleyicinin kendisi için işleme talimatları üretebilmesi için tür adlı bir parametreyi kabul etmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="ccd27-202">Bu, birden çok bileşen arasında yeniden kullanılabilir hale getirmek istiyorsanız, onu üye olarak bildirmeyi göz önünde bulundurun `public static` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="ccd27-203">Bu, artık ilişkisiz bir bileşenden çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="ccd27-204">Bu teknik, bileşen başına ek yük olmadan işlenen yeniden kullanılabilir biçimlendirme kod parçacıklarının kitaplıklarını oluşturmak için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="ccd27-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> Temsilciler Ayrıca parametreleri kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="ccd27-206">Önceki örnekteki bileşenin eşdeğerini oluşturmak için `ChatMessageDisplay` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="ccd27-207">Bu yaklaşım, bileşen başına ek yük olmadan işleme mantığını yeniden kullanma avantajını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="ccd27-208">Ancak, Kullanıcı arabiriminin alt ağacını bağımsız olarak yenileyebilme avantajına sahip değildir veya bileşen sınırı olmadığından, üst öğesi oluştururken Kullanıcı arabiriminin alt ağacını işlemeyi atlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="ccd27-209">Çok fazla parametre almıyorsunuz</span><span class="sxs-lookup"><span data-stu-id="ccd27-209">Don't receive too many parameters</span></span>

<span data-ttu-id="ccd27-210">Bir bileşen, yüzlerce veya binlerce kez çok sık tekrarlarken, her bir parametreyi gönderme ve alma yükünün ne olduğunu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="ccd27-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="ccd27-211">Çok sayıda parametrenin performansı ciddi ölçüde kısıtladığından, ancak bir faktör olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="ccd27-212">`<TableCell>`Bir kılavuz içinde 1.000 kez işleyen bir bileşen için, kendisine geçirilen her ek parametre, toplam işleme maliyetine 15 MS 'yi ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="ccd27-213">Her bir hücre 10 parametre kabul ettiğinde, parametre geçirme her bileşen işleme başına 150 MS 'yi, bu nedenle de 150.000 MS (150 saniye) ve kendi kendine bir bağımsız Kullanıcı arabirimine neden olur.</span><span class="sxs-lookup"><span data-stu-id="ccd27-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="ccd27-214">Bu yükü azaltmak için, özel sınıflar aracılığıyla birden çok parametreyi birlikte paketleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="ccd27-215">Örneğin, bir `<TableCell>` bileşen şunları kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="ccd27-216">Yukarıdaki örnekte, `Data` her hücre için farklıdır, ancak `Options` tümünde ortaktır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="ccd27-217">Kuşkusuz, bir `<TableCell>` bileşenin olmaması ve bunun yerine ana bileşene ait mantığını satır içi olarak bir geliştirme olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="ccd27-218">Geçişli parametrelerin düzeltildiğinden emin olun</span><span class="sxs-lookup"><span data-stu-id="ccd27-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="ccd27-219">`<CascadingValue>`Bileşenin adlı isteğe bağlı bir parametresi vardır `IsFixed` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="ccd27-220">`IsFixed`Değer `false` (varsayılan) ise, basamaklı değerin her alıcısı değişiklik bildirimlerini almak için bir abonelik ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="ccd27-221">Bu durumda, `[CascadingParameter]` abonelik izlemenin nedeni, her biri düzenli olarak **oldukça yüksektir** `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-221">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="ccd27-222">`IsFixed`Değer `true` (örneğin, `<CascadingValue Value="@someValue" IsFixed="true">` ) ise, alıcı başlangıç değerini alır, ancak güncelleştirmeleri almak için herhangi bir abonelik ayarlamayın  .</span><span class="sxs-lookup"><span data-stu-id="ccd27-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="ccd27-223">Bu durumda, her biri `[CascadingParameter]` hafif ve sıradan **daha pahalı** değildir `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="ccd27-224">Mümkün olan yerlerde, `IsFixed="true"` basamaklı değerler üzerinde kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="ccd27-225">Bunu, sağlanan değer zaman içinde değişmeyen her seferinde yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="ccd27-226">Bir bileşenin `this` basamaklı bir değer olarak geçtiği ortak düzende şunu kullanmanız gerekir `IsFixed="true"` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="ccd27-227">Bu, basamaklı değeri alan çok sayıda bileşen varsa büyük bir farklılık yapar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="ccd27-228">Daha fazla bilgi için bkz. <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="ccd27-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="ccd27-229">İle öznitelik azaltma `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="ccd27-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="ccd27-230">Bileşenler, bayrağını kullanarak "eşleşmeyen" parametre değerleri almayı seçebilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> :</span><span class="sxs-lookup"><span data-stu-id="ccd27-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="ccd27-231">Bu yaklaşım, öğesine rastgele ek özniteliklerin geçirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="ccd27-232">Ancak, oluşturucunun şunları gerektirdiğinden de oldukça pahalıdır:</span><span class="sxs-lookup"><span data-stu-id="ccd27-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="ccd27-233">Bir sözlük oluşturmak için, sağlanan parametrelerin tümünü bilinen parametreler kümesiyle eşleştirin.</span><span class="sxs-lookup"><span data-stu-id="ccd27-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="ccd27-234">Aynı özniteliğin birden çok kopyasının birbirinin üzerine yazılmasına devam edin.</span><span class="sxs-lookup"><span data-stu-id="ccd27-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="ccd27-235"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Performans açısından kritik olmayan bileşenlere (çoğunlukla yinelenmeyen olanlar gibi) ücretsiz olarak yararlanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="ccd27-236">Ancak, bir kılavuzdaki büyük bir liste veya hücrelerde bulunan her öğe gibi ölçekli bir şekilde işlenen bileşenler için, öznitelik slatmasını kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="ccd27-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="ccd27-237">Daha fazla bilgi için bkz. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="ccd27-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="ccd27-238">`SetParametersAsync`El ile uygulama</span><span class="sxs-lookup"><span data-stu-id="ccd27-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="ccd27-239">Bileşen başına işleme ek yükünün ana yönlerinden biri, özelliklere gelen parametre değerlerini yazıyor `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="ccd27-240">Bu işlemi yapmak için oluşturucunun yansıma kullanması vardır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="ccd27-241">Bu biraz iyileştirilse de, WebAssembly çalışma zamanında JıT desteğinin yokluğu sınırlar uygular.</span><span class="sxs-lookup"><span data-stu-id="ccd27-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="ccd27-242">Bazı olağanüstü durumlarda, yansıma kullanmaktan kaçınmak ve kendi parametre ayarı mantığınızı el ile uygulamak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="ccd27-243">Bu, şu durumlarda uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-243">This may be applicable when:</span></span>

 * <span data-ttu-id="ccd27-244">Çok sık işleyen bir bileşeniniz var (örneğin, Kullanıcı arabiriminde yüzlerce veya binlerce kopyası vardır).</span><span class="sxs-lookup"><span data-stu-id="ccd27-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="ccd27-245">Birçok parametre kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ccd27-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="ccd27-246">Parametre alma yükünün Kullanıcı arabirimi yanıtlama hızı üzerinde bir observable etkisi olduğunu fark edersiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="ccd27-247">Bu durumlarda, bileşenin sanal yöntemini geçersiz kılabilir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ve kendi bileşenine özgü mantığınızı uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="ccd27-248">Aşağıdaki örnek, tüm sözlük aramalarını kasıtlı olarak engeller:</span><span class="sxs-lookup"><span data-stu-id="ccd27-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="ccd27-249">Yukarıdaki kodda, temel sınıfı döndürmek, <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> parametreleri yeniden atamadan normal yaşam döngüsü yöntemlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="ccd27-250">Yukarıdaki kodda görebileceğiniz gibi, <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> özel mantığı geçersiz kılma ve sağlama, karmaşık ve laborious, bu nedenle bu yaklaşım genel olarak önerilmez.</span><span class="sxs-lookup"><span data-stu-id="ccd27-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="ccd27-251">Olağanüstü durumlarda, işleme performansını% 20-25 oranında iyileştirebilir, ancak bu yaklaşımı yalnızca daha önce listelenen senaryolarda düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="ccd27-252">Olayları çok hızlı tetiklemez</span><span class="sxs-lookup"><span data-stu-id="ccd27-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="ccd27-253">Bazı tarayıcı olayları son derece sıklıkla `onmousemove` (örneğin, saniyede) çok sık harekete geçmektedir `onscroll` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="ccd27-254">Çoğu durumda, bu sıklıkla Kullanıcı Arabirimi güncelleştirmeleri gerçekleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ccd27-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="ccd27-255">Bunu yapmayı denerseniz, Kullanıcı Arabirimi yanıt verme veya aşırı CPU süresi tükettiğiniz için zararlı olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="ccd27-256">Yerel `@onmousemove` veya olayları kullanmak yerine `@onscroll` , daha az sıklıkta başlatılan bir geri aramayı kaydetmek için js birlikte çalışabilirliği kullanmayı tercih edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="ccd27-257">Örneğin, aşağıdaki bileşen ( `MyComponent.razor` ) fare konumunu, ancak her 500 MS için yalnızca en fazla güncelleştirme olduğunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="ccd27-258">Sayfaya yerleştirilebilecek `index.html` veya BIR ES6 modülü olarak yüklenen Ilgili JavaScript kodu, gerçek DOM olay dinleyicisini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ccd27-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="ccd27-259">Bu örnekte, olay dinleyicisi, çağırma oranını sınırlamak için [Lodash `throttle` işlevini](https://lodash.com/docs/4.17.15#throttle) kullanır:</span><span class="sxs-lookup"><span data-stu-id="ccd27-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="ccd27-260">Blazor ServerHer olay çağrısı ağ üzerinden bir ileti teslimi içerdiğinden, bu teknik daha da önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-260">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="ccd27-261">Blazor WebAssemblyİşleme işinin miktarını önemli ölçüde azaltabildiğinden, için bu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-261">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="ccd27-262">JavaScript birlikte çalışma hızını iyileştirme</span><span class="sxs-lookup"><span data-stu-id="ccd27-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="ccd27-263">.NET ve JavaScript arasındaki çağrılar bazı ek yük içerir, çünkü:</span><span class="sxs-lookup"><span data-stu-id="ccd27-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="ccd27-264">Varsayılan olarak, çağrılar zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="ccd27-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="ccd27-265">Varsayılan olarak, parametreler ve dönüş değerleri JSON serileştirilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="ccd27-266">Bu, .NET ve JavaScript türleri arasında kolay anlaşılır bir dönüştürme mekanizması sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="ccd27-267">Ayrıca Blazor Server , bu çağrılar ağ üzerinden geçirilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-267">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="ccd27-268">Aşırı hassas çağrılardan kaçının</span><span class="sxs-lookup"><span data-stu-id="ccd27-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="ccd27-269">Her çağrı bazı ek yük içerdiğinden, çağrı sayısını azaltmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="ccd27-270">Tarayıcı deposunda bir öğe koleksiyonunu depolayan aşağıdaki kodu göz önünde bulundurun `localStorage` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="ccd27-271">Yukarıdaki örnek her öğe için ayrı bir JS birlikte çalışma çağrısı yapar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="ccd27-272">Bunun yerine, aşağıdaki yaklaşım, JS birlikte çalışabilirliğine tek bir çağrı azaltır:</span><span class="sxs-lookup"><span data-stu-id="ccd27-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="ccd27-273">Karşılık gelen JavaScript işlevi aşağıdaki gibi tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="ccd27-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="ccd27-274">İçin Blazor WebAssembly , bu genellikle çok SAYıDA js birlikte çalışma çağrısı yapıyorsanız önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-274">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="ccd27-275">Zaman uyumlu çağrılar yapmayı düşünün</span><span class="sxs-lookup"><span data-stu-id="ccd27-275">Consider making synchronous calls</span></span>

<span data-ttu-id="ccd27-276">Çağrılan kodun zaman uyumlu veya zaman uyumsuz olmasına bakılmaksızın, JavaScript birlikte çalışma çağrıları varsayılan olarak zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="ccd27-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="ccd27-277">Bu, bileşenlerin hem hem de ile uyumlu olmasını Blazor WebAssembly sağlamaktır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="ccd27-277">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="ccd27-278">Üzerinde Blazor Server , bir ağ bağlantısı üzerinden gönderildiğinden tüm JavaScript birlikte çalışma çağrılarının zaman uyumsuz olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-278">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="ccd27-279">Uygulamanızın yalnızca üzerinde çalıştırıldıklarından emin değilseniz Blazor WebAssembly , zaman uyumlu JavaScript birlikte çalışma çağrıları yapmayı tercih edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-279">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="ccd27-280">Bu, zaman uyumsuz çağrılar yapmaktan biraz daha az yüke sahiptir ve sonuçları beklerken ara durum olmadığından daha az işleme döngülerine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="ccd27-281">.NET 'ten JavaScript 'e zaman uyumlu bir çağrı yapmak için şu noktaya <xref:Microsoft.JSInterop.IJSRuntime> geçin <xref:Microsoft.JSInterop.IJSInProcessRuntime> :</span><span class="sxs-lookup"><span data-stu-id="ccd27-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ccd27-282">İle çalışırken `IJSObjectReference` , ' ye atama yaparak zaman uyumlu bir çağrı yapabilirsiniz `IJSInProcessObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="ccd27-283">JavaScript 'ten .NET 'e zaman uyumlu bir çağrı yapmak için `DotNet.invokeMethod` yerine kullanın `DotNet.invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="ccd27-284">Zaman uyumlu çağrılar şu durumlarda çalışır:</span><span class="sxs-lookup"><span data-stu-id="ccd27-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="ccd27-285">Uygulama üzerinde çalışıyor Blazor WebAssembly , değil Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="ccd27-285">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="ccd27-286">Çağrılan işlev, zaman uyumlu bir değer döndürür (bir `async` yöntem değildir ve bir .net <xref:System.Threading.Tasks.Task> veya JavaScript döndürmez `Promise` ).</span><span class="sxs-lookup"><span data-stu-id="ccd27-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="ccd27-287">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="ccd27-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="ccd27-288">Sıradan olmayan çağrılar yapmayı düşünün</span><span class="sxs-lookup"><span data-stu-id="ccd27-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="ccd27-289">Üzerinde çalışırken Blazor WebAssembly , .net 'Ten JavaScript 'e sıradan geri çağrı yapmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="ccd27-289">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="ccd27-290">Bunlar bağımsız değişkenlerin JSON serileştirmesini veya dönüş değerlerini gerçekleştirmemenizi zaman uyumlu çağrılardır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="ccd27-291">Bellek yönetiminin ve .NET ve JavaScript temsilleri arasındaki çevirilerin her yönü, geliştiriciye kalmadı.</span><span class="sxs-lookup"><span data-stu-id="ccd27-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="ccd27-292">Kullanırken `IJSUnmarshalledRuntime` , JS birlikte çalışma yaklaşımının en az ek yükü olsa da, bu API 'lerle etkileşimde bulunmak için gereken JavaScript API 'leri Şu anda açıklanmamıştır ve gelecekteki sürümlerde yapılan değişikliklere tabidir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="ccd27-293">Uygulama indirme boyutunu en aza indir</span><span class="sxs-lookup"><span data-stu-id="ccd27-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="ccd27-294">Ara dil (IL) kırpma</span><span class="sxs-lookup"><span data-stu-id="ccd27-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="ccd27-295">[Kullanılmayan derlemelerin bir Blazor WebAssembly uygulamadan bölünmesi](xref:blazor/host-and-deploy/configure-trimmer) , uygulamanın ikili dosyalarında kullanılmayan kodu kaldırarak uygulamanın boyutunu küçültür.</span><span class="sxs-lookup"><span data-stu-id="ccd27-295">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="ccd27-296">Varsayılan olarak, bir uygulama yayımlanırken ayarlayıcısı yürütülür.</span><span class="sxs-lookup"><span data-stu-id="ccd27-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="ccd27-297">Kırpma özelliğinden faydalanmak için, [`dotnet publish`](/dotnet/core/tools/dotnet-publish) komutunu kullanarak [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan uygulamayı dağıtım için yayımlayın `Release` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="ccd27-298">Ara dil (IL) bağlama</span><span class="sxs-lookup"><span data-stu-id="ccd27-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="ccd27-299">Uygulamanın [bağlanması Blazor WebAssembly ](xref:blazor/host-and-deploy/configure-linker) , uygulamanın ikili dosyalarında kullanılmayan kodu kırparak uygulamanın boyutunu azaltır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-299">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="ccd27-300">Varsayılan olarak, ara dil (IL) bağlayıcı yalnızca yapılandırmada oluşturulurken etkindir `Release` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="ccd27-301">Bu özellikten yararlanmak için, [`dotnet publish`](/dotnet/core/tools/dotnet-publish) [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan komutunu kullanarak uygulamayı dağıtım için yayımlayın `Release` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="ccd27-302">Üzerinde System.Text.Jskullan</span><span class="sxs-lookup"><span data-stu-id="ccd27-302">Use System.Text.Json</span></span>

<span data-ttu-id="ccd27-303">Blazor' nin JS birlikte çalışma uygulamasının <xref:System.Text.Json> , düşük bellek ayırması olan yüksek performanslı BIR JSON serileştirme kitaplığı olan ' i kullanır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-303">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="ccd27-304">Kullanmak <xref:System.Text.Json> , bir veya daha fazla ALTERNATIF JSON kitaplığı ekleyerek ek uygulama yükü boyutuna neden olmaz.</span><span class="sxs-lookup"><span data-stu-id="ccd27-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="ccd27-305">Geçiş Kılavuzu için, bkz. ' [den `Newtonsoft.Json` `System.Text.Json` ' ye geçiş ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="ccd27-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="ccd27-306">Yavaş yük derlemeleri</span><span class="sxs-lookup"><span data-stu-id="ccd27-306">Lazy load assemblies</span></span>

<span data-ttu-id="ccd27-307">Derlemeler bir rota için gerektiğinde çalışma zamanında derlemeleri yükleyin.</span><span class="sxs-lookup"><span data-stu-id="ccd27-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="ccd27-308">Daha fazla bilgi için bkz. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="ccd27-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="ccd27-309">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="ccd27-309">Compression</span></span>

<span data-ttu-id="ccd27-310">Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-310">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="ccd27-311">Blazor , içerik eklemek için sunucuyu kullanır ve statik olarak sıkıştırılan dosyaları sunar.</span><span class="sxs-lookup"><span data-stu-id="ccd27-311">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="ccd27-312">Bir uygulama dağıtıldıktan sonra uygulamanın sıkıştırılmış dosyalara hizmet ettiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ccd27-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="ccd27-313">Tarayıcının Geliştirici Araçları ağ sekmesini inceleyin ve dosyaların veya ile birlikte sunulduğunu doğrulayın `Content-Encoding: br` `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="ccd27-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="ccd27-314">Ana bilgisayar sıkıştırılmış dosyalara hizmet vermemişse, içindeki yönergeleri izleyin <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="ccd27-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="ccd27-315">Kullanılmayan özellikleri devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="ccd27-315">Disable unused features</span></span>

<span data-ttu-id="ccd27-316">Blazor WebAssemblyçalışma zamanı, uygulamanın daha küçük bir yük boyutu için gerektirmemesi durumunda devre dışı bırakılabilen aşağıdaki .NET özelliklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="ccd27-316">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="ccd27-317">Saat dilimi bilgilerinin doğru olması için bir veri dosyası dahildir.</span><span class="sxs-lookup"><span data-stu-id="ccd27-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="ccd27-318">Uygulama bu özelliği gerektirmiyorsa, `BlazorEnableTimeZoneSupport` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-318">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="ccd27-319">Varsayılan olarak, Blazor WebAssembly Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken Genelleştirme kaynaklarını taşır.</span><span class="sxs-lookup"><span data-stu-id="ccd27-319">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="ccd27-320">Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan [sabit kültürü destekleyecek şekilde yapılandırabilirsiniz](xref:blazor/globalization-localization) `en-US` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="ccd27-321">Doğru çalışma gibi API 'Leri oluşturmak için harmanlama bilgileri eklenmiştir <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ccd27-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="ccd27-322">Uygulamanın harmanlama verileri gerektirmediğinden emin değilseniz, `BlazorWebAssemblyPreserveCollationData` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :</span><span class="sxs-lookup"><span data-stu-id="ccd27-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
