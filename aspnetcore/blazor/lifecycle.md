---
title: ASP.NET Core Blazor yaşam döngüsü
author: guardrex
description: ASP.NET Core Blazor uygulamalarda bileşen yaşam Razor döngüsü yöntemlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 571f14247efe08ac6abbd6d1e2720656f94c213c
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967460"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="0b6fb-103">ASP.NET Core Blazor yaşam döngüsü</span><span class="sxs-lookup"><span data-stu-id="0b6fb-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="0b6fb-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="0b6fb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0b6fb-105">Çerçeve Blazor , zaman uyumlu ve zaman uyumsuz yaşam döngüsü yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="0b6fb-106">Bileşen başlatma ve işleme sırasında bileşenlerde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="0b6fb-107">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0b6fb-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="0b6fb-108">Bileşen başlatma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0b6fb-108">Component initialization methods</span></span>

<span data-ttu-id="0b6fb-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> , bileşen ilk parametrelerini ana bileşeninden aldıktan sonra başlatıldığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="0b6fb-110">Bileşen `OnInitializedAsync` zaman uyumsuz bir işlem gerçekleştirdiğinde ve işlem tamamlandığında yenilenmesi gerektiğinde kullanın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="0b6fb-111">Zaman uyumlu bir işlem için, `OnInitialized`geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="0b6fb-112">Zaman uyumsuz bir işlem gerçekleştirmek için, `OnInitializedAsync` işlem üzerinde `await` anahtar sözcüğünü geçersiz kılın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="0b6fb-113">[İçerik](xref:blazor/hosting-model-configuration#render-mode) araması `OnInitializedAsync` yapan sunucu uygulamaları **_iki kez_**.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="0b6fb-114">Bir kez, bileşen sayfanın bir parçası olarak başlangıçta statik olarak işlendiğinde.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="0b6fb-115">Tarayıcı sunucuya geri bir bağlantı kurduğunda ikinci bir zaman.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="0b6fb-116">İçindeki `OnInitializedAsync` geliştirici kodunun iki kez çalıştırılmasını engellemek için [prerendering sonrasında durum bilgisi olan yeniden bağlanma](#stateful-reconnection-after-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="0b6fb-117">Blazor Sunucu uygulaması prerendering olsa da, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="0b6fb-118">Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="0b6fb-119">Daha fazla bilgi için bkz. [uygulamanın ne zaman prerendering](#detect-when-the-app-is-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="0b6fb-120">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0b6fb-121">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="0b6fb-122">Parametreler ayarlanmadan önce</span><span class="sxs-lookup"><span data-stu-id="0b6fb-122">Before parameters are set</span></span>

<span data-ttu-id="0b6fb-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>işleme ağacındaki bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="0b6fb-124"><xref:Microsoft.AspNetCore.Components.ParameterView>her çağrılışında `SetParametersAsync` parametre değerleri kümesinin tamamını içerir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="0b6fb-125">Varsayılan `SetParametersAsync` uygulama, `[Parameter]` `ParameterView`her bir özelliğin değerini içinde karşılık gelen bir değere sahip `[CascadingParameter]` veya özniteliğiyle ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="0b6fb-126">İçinde `ParameterView` karşılık gelen bir değere sahip olmayan parametreler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="0b6fb-127">Çağrılmadıysa `base.SetParametersAync` , özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="0b6fb-128">Örneğin, sınıftaki özelliklere gelen parametreleri atama gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="0b6fb-129">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0b6fb-130">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="0b6fb-131">Parametreler ayarlandıktan sonra</span><span class="sxs-lookup"><span data-stu-id="0b6fb-131">After parameters are set</span></span>

<span data-ttu-id="0b6fb-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> şu şekilde adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="0b6fb-133">Bileşen başlatıldığında ve üst bileşeninden ilk parametre kümesini aldığında.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="0b6fb-134">Üst bileşen yeniden oluşturup şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="0b6fb-135">Yalnızca en az bir parametresinin değiştiği, bilinen temel sabit türler.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="0b6fb-136">Tüm karmaşık türsüz parametreler.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-136">Any complex-typed parameters.</span></span> <span data-ttu-id="0b6fb-137">Çerçeve, karmaşık yazılmış bir parametrenin değerlerinin dahili olarak değişip değişmediğini bilmez, bu yüzden parametre kümesini değiştirilmiş olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="0b6fb-138">`OnParametersSetAsync` Yaşam döngüsü olayında parametre ve özellik değerleri uygulanırken zaman uyumsuz iş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="0b6fb-139">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0b6fb-140">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="0b6fb-141">Bileşen oluşturulduktan sonra</span><span class="sxs-lookup"><span data-stu-id="0b6fb-141">After component render</span></span>

<span data-ttu-id="0b6fb-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> bir bileşen işlemeyi tamamladıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="0b6fb-143">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="0b6fb-144">İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="0b6fb-145">Ve `firstRender` `OnAfterRender`için `OnAfterRenderAsync` parametresi:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="0b6fb-146">, Bileşen örneği `true` ilk kez işlendiğinde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="0b6fb-147">Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="0b6fb-148">`OnAfterRenderAsync` Yaşam döngüsü olayında işleme hemen sonra zaman uyumsuz çalışma gerçekleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="0b6fb-149">Bir <xref:System.Threading.Tasks.Task> öğesinden `OnAfterRenderAsync`döndürseniz bile, çerçeve bu görev tamamlandıktan sonra bileşen için başka bir işleme çevrimi zamanlayamaz.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="0b6fb-150">Bu, sonsuz bir işleme döngüsünden kaçınmaktır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="0b6fb-151">Diğer yaşam döngüsü yöntemlerinden farklı olduğundan, döndürülen görev tamamlandığında daha fazla işleme döngüsü zamanlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="0b6fb-152">`OnAfterRender`ve `OnAfterRenderAsync` *sunucuda prerendering çağrıldığında çağrılmaz.*</span><span class="sxs-lookup"><span data-stu-id="0b6fb-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="0b6fb-153">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0b6fb-154">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="0b6fb-155">UI yenilemeyi bastır</span><span class="sxs-lookup"><span data-stu-id="0b6fb-155">Suppress UI refreshing</span></span>

<span data-ttu-id="0b6fb-156">UI <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> yenilemeyi gizlemek için geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="0b6fb-157">Uygulama döndürürse `true`, Kullanıcı arabirimi yenilenir:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="0b6fb-158">`ShouldRender`bileşen her işlendiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="0b6fb-159">`ShouldRender` Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="0b6fb-160">Durum değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="0b6fb-160">State changes</span></span>

<span data-ttu-id="0b6fb-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>bileşene durumunun değiştiğini bildirir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="0b6fb-162">Uygun olduğunda, çağırma `StateHasChanged` bileşenin yeniden yönlendirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="0b6fb-163">İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle</span><span class="sxs-lookup"><span data-stu-id="0b6fb-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="0b6fb-164">Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="0b6fb-165">Yaşam döngüsü yöntemi `null` yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="0b6fb-166">Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="0b6fb-167">Nesneler olduğunda `null`yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="0b6fb-168">Blazor Şablonların `FetchData` bileşeninde, `OnInitializedAsync` Asychronously (`forecasts`) tahmin verileri almak için geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="0b6fb-169">Ne `forecasts` zaman `null`olduğunda, kullanıcıya bir yükleme iletisi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="0b6fb-170">İşlem `OnInitializedAsync` tamamlandıktan `Task` sonra, bileşen güncelleştirilmiş duruma geri döner.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="0b6fb-171">Blazor Sunucu şablonunda *Pages/fetchdata. Razor* :</span><span class="sxs-lookup"><span data-stu-id="0b6fb-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="0b6fb-172">IDisposable ile bileşen atma</span><span class="sxs-lookup"><span data-stu-id="0b6fb-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="0b6fb-173">Bir bileşen uygularsa <xref:System.IDisposable>, bileşen kullanıcı arabiriminden kaldırıldığında [Dispose yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağırılır.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="0b6fb-174">Aşağıdaki bileşen ve `Dispose` yöntemini `@implements IDisposable` kullanır:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="0b6fb-175">' <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> In `Dispose` çağrılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="0b6fb-176">`StateHasChanged`oluşturucuyu aşağı doğru olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmelerinin kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="0b6fb-177">.NET etkinliklerinden olay işleyicilerini kaldırma.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="0b6fb-178">Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, `Dispose` yönteminde bir olay işleyicisinin nasıl geri yükleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="0b6fb-179">Özel alan ve lambda yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="0b6fb-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="0b6fb-180">Özel yöntem yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="0b6fb-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="0b6fb-181">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="0b6fb-181">Handle errors</span></span>

<span data-ttu-id="0b6fb-182">Yaşam döngüsü yöntemi yürütme sırasında hataları işleme hakkında bilgi için bkz <xref:blazor/handle-errors#lifecycle-methods>..</span><span class="sxs-lookup"><span data-stu-id="0b6fb-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="0b6fb-183">Prerendering sonrasında durum bilgisi olan yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="0b6fb-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="0b6fb-184">Bir Blazor sunucu `RenderMode` `ServerPrerendered`uygulamasında,, bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="0b6fb-185">Tarayıcı sunucuya geri bir bağlantı kurduğunda, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="0b6fb-186">Bileşeni başlatmak için [Onbaşlatılmış {Async}](#component-initialization-methods) yaşam döngüsü yöntemi varsa, yöntem *iki kez*yürütülür:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="0b6fb-187">Bileşen statik olarak önceden kullanılırken.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="0b6fb-188">Sunucu bağlantısı kurulduktan sonra.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-188">After the server connection has been established.</span></span>

<span data-ttu-id="0b6fb-189">Bu, bileşen son işlendiğinde Kullanıcı arabiriminde görünen verilerde fark edilebilir bir değişikliğe neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="0b6fb-190">Bir Blazor sunucu uygulamasında çift işleme senaryosunu önlemek için:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="0b6fb-191">Prerendering sırasında durumu önbelleğe almak için kullanılabilecek bir tanımlayıcı geçirin ve uygulamayı yeniden başlattıktan sonra durumu alma.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="0b6fb-192">Bileşen durumunu kaydetmek için prerendering sırasında tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="0b6fb-193">Önbelleğe alınan durumu almak için prerendering öğesinden sonra tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0b6fb-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="0b6fb-194">Aşağıdaki kod, bir şablon tabanlı `WeatherForecastService` Blazor sunucu uygulamasında, Çift işlemeyi engelleyen güncelleştirilmiş bir güncelleme göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="0b6fb-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="0b6fb-195">Hakkında `RenderMode`daha fazla bilgi için bkz <xref:blazor/hosting-model-configuration#render-mode>..</span><span class="sxs-lookup"><span data-stu-id="0b6fb-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="0b6fb-196">Uygulamanın ne zaman prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="0b6fb-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
