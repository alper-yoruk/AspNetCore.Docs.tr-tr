---
title: ASP.NET Core Blazor yaşam döngüsü
author: guardrex
description: RazorASP.NET Core uygulamalarda bileşen yaşam döngüsü yöntemlerini nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e4fcd86b6e6a84d9e34a83688f9fb80c6907e5f3
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438921"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="6ceac-103">ASP.NET Core Blazor yaşam döngüsü</span><span class="sxs-lookup"><span data-stu-id="6ceac-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="6ceac-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="6ceac-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6ceac-105">BlazorÇerçeve, zaman uyumlu ve zaman uyumsuz yaşam döngüsü yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="6ceac-106">Bileşen başlatma ve işleme sırasında bileşenlerde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="6ceac-107">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="6ceac-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="6ceac-108">Bileşen başlatma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="6ceac-108">Component initialization methods</span></span>

<span data-ttu-id="6ceac-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>ve, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> bileşen ilk parametrelerini ana bileşeninden aldıktan sonra başlatıldığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="6ceac-110">`OnInitializedAsync`Bileşen zaman uyumsuz bir işlem gerçekleştirdiğinde ve işlem tamamlandığında yenilenmesi gerektiğinde kullanın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="6ceac-111">Zaman uyumlu bir işlem için, geçersiz kılın `OnInitialized` :</span><span class="sxs-lookup"><span data-stu-id="6ceac-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="6ceac-112">Zaman uyumsuz bir işlem gerçekleştirmek için, `OnInitializedAsync` `await` işlem üzerinde anahtar sözcüğünü geçersiz kılın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="6ceac-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="6ceac-113">[İçerik](xref:blazor/hosting-model-configuration#render-mode) araması yapan sunucu uygulamaları `OnInitializedAsync` **_iki kez_**.</span><span class="sxs-lookup"><span data-stu-id="6ceac-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="6ceac-114">Bir kez, bileşen sayfanın bir parçası olarak başlangıçta statik olarak işlendiğinde.</span><span class="sxs-lookup"><span data-stu-id="6ceac-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="6ceac-115">Tarayıcı sunucuya geri bir bağlantı kurduğunda ikinci bir zaman.</span><span class="sxs-lookup"><span data-stu-id="6ceac-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="6ceac-116">İçindeki geliştirici kodunun `OnInitializedAsync` iki kez çalıştırılmasını engellemek için [prerendering sonrasında durum bilgisi olan yeniden bağlanma](#stateful-reconnection-after-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="6ceac-117">BlazorSunucu uygulaması prerendering olsa da, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="6ceac-118">Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="6ceac-119">Daha fazla bilgi için bkz. [uygulamanın ne zaman prerendering](#detect-when-the-app-is-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="6ceac-120">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ceac-121">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="6ceac-122">Parametreler ayarlanmadan önce</span><span class="sxs-lookup"><span data-stu-id="6ceac-122">Before parameters are set</span></span>

<span data-ttu-id="6ceac-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>işleme ağacındaki bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="6ceac-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="6ceac-124"><xref:Microsoft.AspNetCore.Components.ParameterView>her çağrılışında parametre değerleri kümesinin tamamını içerir `SetParametersAsync` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="6ceac-125">Varsayılan uygulama `SetParametersAsync` , her bir özelliğin değerini `[Parameter]` `[CascadingParameter]` içinde karşılık gelen bir değere sahip veya özniteliğiyle ayarlar `ParameterView` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="6ceac-126">İçinde karşılık gelen bir değere sahip olmayan parametreler `ParameterView` değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="6ceac-127">`base.SetParametersAync`Çağrılmadıysa, özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="6ceac-128">Örneğin, sınıftaki özelliklere gelen parametreleri atama gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6ceac-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="6ceac-129">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ceac-130">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="6ceac-131">Parametreler ayarlandıktan sonra</span><span class="sxs-lookup"><span data-stu-id="6ceac-131">After parameters are set</span></span>

<span data-ttu-id="6ceac-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> Şu şekilde adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="6ceac-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="6ceac-133">Bileşen başlatıldığında ve üst bileşeninden ilk parametre kümesini aldığında.</span><span class="sxs-lookup"><span data-stu-id="6ceac-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="6ceac-134">Üst bileşen yeniden oluşturup şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="6ceac-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="6ceac-135">Yalnızca en az bir parametresinin değiştiği, bilinen temel sabit türler.</span><span class="sxs-lookup"><span data-stu-id="6ceac-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="6ceac-136">Tüm karmaşık türsüz parametreler.</span><span class="sxs-lookup"><span data-stu-id="6ceac-136">Any complex-typed parameters.</span></span> <span data-ttu-id="6ceac-137">Çerçeve, karmaşık yazılmış bir parametrenin değerlerinin dahili olarak değişip değişmediğini bilmez, bu yüzden parametre kümesini değiştirilmiş olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="6ceac-138">Yaşam döngüsü olayında parametre ve özellik değerleri uygulanırken zaman uyumsuz iş olması gerekir `OnParametersSetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="6ceac-139">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ceac-140">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="6ceac-141">Bileşen oluşturulduktan sonra</span><span class="sxs-lookup"><span data-stu-id="6ceac-141">After component render</span></span>

<span data-ttu-id="6ceac-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> bir bileşen işlemeyi tamamladıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="6ceac-143">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="6ceac-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="6ceac-144">İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="6ceac-145">`firstRender`Ve için parametresi `OnAfterRenderAsync` `OnAfterRender` :</span><span class="sxs-lookup"><span data-stu-id="6ceac-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="6ceac-146">, `true` Bileşen örneği ilk kez işlendiğinde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="6ceac-147">Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="6ceac-148">Yaşam döngüsü olayında işleme hemen sonra zaman uyumsuz çalışma gerçekleşmelidir `OnAfterRenderAsync` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="6ceac-149">Bir öğesinden döndürseniz bile <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync` , çerçeve bu görev tamamlandıktan sonra bileşen için başka bir işleme çevrimi zamanlayamaz.</span><span class="sxs-lookup"><span data-stu-id="6ceac-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="6ceac-150">Bu, sonsuz bir işleme döngüsünden kaçınmaktır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="6ceac-151">Diğer yaşam döngüsü yöntemlerinden farklı olduğundan, döndürülen görev tamamlandığında daha fazla işleme döngüsü zamanlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6ceac-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="6ceac-152">`OnAfterRender`ve `OnAfterRenderAsync` *sunucuda prerendering çağrıldığında çağrılmaz.*</span><span class="sxs-lookup"><span data-stu-id="6ceac-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="6ceac-153">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ceac-154">Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="6ceac-155">UI yenilemeyi bastır</span><span class="sxs-lookup"><span data-stu-id="6ceac-155">Suppress UI refreshing</span></span>

<span data-ttu-id="6ceac-156"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>UI yenilemeyi gizlemek için geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="6ceac-157">Uygulama döndürürse `true` , Kullanıcı arabirimi yenilenir:</span><span class="sxs-lookup"><span data-stu-id="6ceac-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="6ceac-158">`ShouldRender`bileşen her işlendiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="6ceac-159">`ShouldRender`Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="6ceac-160">Daha fazla bilgi için bkz. <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="6ceac-160">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="6ceac-161">Durum değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="6ceac-161">State changes</span></span>

<span data-ttu-id="6ceac-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>bileşene durumunun değiştiğini bildirir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="6ceac-163">Uygun olduğunda, çağırma `StateHasChanged` bileşenin yeniden yönlendirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="6ceac-163">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="6ceac-164">İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle</span><span class="sxs-lookup"><span data-stu-id="6ceac-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="6ceac-165">Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="6ceac-166">`null`Yaşam döngüsü yöntemi yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="6ceac-167">Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="6ceac-168">Nesneler olduğunda yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme `null` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="6ceac-169">`FetchData` Blazor Şablonların bileşeninde, `OnInitializedAsync` Asychronously () tahmin verileri almak için geçersiz kılınır `forecasts` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-169">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="6ceac-170">Ne zaman olduğunda `forecasts` `null` , kullanıcıya bir yükleme iletisi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="6ceac-171">`Task` `OnInitializedAsync` İşlem tamamlandıktan sonra, bileşen güncelleştirilmiş duruma geri döner.</span><span class="sxs-lookup"><span data-stu-id="6ceac-171">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="6ceac-172">Sunucu şablonunda *Pages/FetchData. Razor* Blazor :</span><span class="sxs-lookup"><span data-stu-id="6ceac-172">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="6ceac-173">IDisposable ile bileşen atma</span><span class="sxs-lookup"><span data-stu-id="6ceac-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="6ceac-174">Bir bileşen uygularsa <xref:System.IDisposable> , bileşen kullanıcı arabiriminden kaldırıldığında [Dispose yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağırılır.</span><span class="sxs-lookup"><span data-stu-id="6ceac-174">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="6ceac-175">Aşağıdaki bileşen `@implements IDisposable` ve `Dispose` yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="6ceac-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="6ceac-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>' In çağrılması `Dispose` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="6ceac-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="6ceac-177">`StateHasChanged`oluşturucuyu aşağı doğru olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmelerinin kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="6ceac-177">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="6ceac-178">.NET etkinliklerinden olay işleyicilerini kaldırma.</span><span class="sxs-lookup"><span data-stu-id="6ceac-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="6ceac-179">Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, yönteminde bir olay işleyicisinin nasıl geri yükleneceğini göstermektedir `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="6ceac-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="6ceac-180">Özel alan ve lambda yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="6ceac-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="6ceac-181">Özel yöntem yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="6ceac-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="6ceac-182">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="6ceac-182">Handle errors</span></span>

<span data-ttu-id="6ceac-183">Yaşam döngüsü yöntemi yürütme sırasında hataları işleme hakkında bilgi için bkz <xref:blazor/handle-errors#lifecycle-methods> ..</span><span class="sxs-lookup"><span data-stu-id="6ceac-183">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="6ceac-184">Prerendering sonrasında durum bilgisi olan yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="6ceac-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="6ceac-185">Bir sunucu uygulamasında,, Blazor `RenderMode` `ServerPrerendered` bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-185">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="6ceac-186">Tarayıcı sunucuya geri bir bağlantı kurduğunda, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur.</span><span class="sxs-lookup"><span data-stu-id="6ceac-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="6ceac-187">Bileşeni başlatmak için [Onbaşlatılmış {Async}](#component-initialization-methods) yaşam döngüsü yöntemi varsa, yöntem *iki kez*yürütülür:</span><span class="sxs-lookup"><span data-stu-id="6ceac-187">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="6ceac-188">Bileşen statik olarak önceden kullanılırken.</span><span class="sxs-lookup"><span data-stu-id="6ceac-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="6ceac-189">Sunucu bağlantısı kurulduktan sonra.</span><span class="sxs-lookup"><span data-stu-id="6ceac-189">After the server connection has been established.</span></span>

<span data-ttu-id="6ceac-190">Bu, bileşen son işlendiğinde Kullanıcı arabiriminde görünen verilerde fark edilebilir bir değişikliğe neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="6ceac-191">Bir sunucu uygulamasında çift işleme senaryosunu önlemek için Blazor :</span><span class="sxs-lookup"><span data-stu-id="6ceac-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="6ceac-192">Prerendering sırasında durumu önbelleğe almak için kullanılabilecek bir tanımlayıcı geçirin ve uygulamayı yeniden başlattıktan sonra durumu alma.</span><span class="sxs-lookup"><span data-stu-id="6ceac-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="6ceac-193">Bileşen durumunu kaydetmek için prerendering sırasında tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="6ceac-194">Önbelleğe alınan durumu almak için prerendering öğesinden sonra tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="6ceac-195">Aşağıdaki kod, bir `WeatherForecastService` şablon tabanlı Blazor sunucu uygulamasında, Çift işlemeyi engelleyen güncelleştirilmiş bir güncelleme göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="6ceac-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="6ceac-196">Hakkında daha fazla bilgi için `RenderMode` bkz <xref:blazor/hosting-model-configuration#render-mode> ..</span><span class="sxs-lookup"><span data-stu-id="6ceac-196">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="6ceac-197">Uygulamanın ne zaman prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="6ceac-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="6ceac-198">İptal edilebilen arka plan çalışması</span><span class="sxs-lookup"><span data-stu-id="6ceac-198">Cancelable background work</span></span>

<span data-ttu-id="6ceac-199">Bileşenler genellikle ağ çağrıları yapma ( <xref:System.Net.Http.HttpClient> ) ve veritabanlarıyla etkileşim kurma gibi uzun süre çalışan arka plan işleri gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="6ceac-200">Çeşitli durumlarda sistem kaynaklarını korumak için arka plan işinin durdurulması istenebilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="6ceac-201">Örneğin, bir Kullanıcı bir bileşenden uzaklaştığında arka planda zaman uyumsuz işlemler otomatik olarak durdurulur.</span><span class="sxs-lookup"><span data-stu-id="6ceac-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="6ceac-202">Arka plan iş öğelerinin iptal etme gerektirmesinin diğer nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6ceac-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="6ceac-203">Hatalı giriş verileriyle veya işleme parametreleriyle çalışan bir arka plan görevi başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="6ceac-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="6ceac-204">Çalışan arka plan iş öğelerinin geçerli kümesi, yeni bir iş öğesi kümesiyle değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="6ceac-205">Yürütülmekte olan görevlerin önceliği değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="6ceac-206">Uygulamanın sunucuya yeniden dağıtılması için kapatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="6ceac-207">Sunucu kaynakları sınırlı hale gelir, tasarımda iş öğelerinin yeniden çizelgeleşmesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6ceac-207">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="6ceac-208">Bir bileşene iptal edilebilen bir arka plan çalışma deseninin uygulanması için:</span><span class="sxs-lookup"><span data-stu-id="6ceac-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="6ceac-209">Ve kullanın <xref:System.Threading.CancellationTokenSource> <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="6ceac-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="6ceac-210">[Bileşenin elden çıkarılmasında](#component-disposal-with-idisposable) ve herhangi bir noktada iptal işlemi, belirteci el ile iptal ederek istenir [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A)</span><span class="sxs-lookup"><span data-stu-id="6ceac-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="6ceac-211">Zaman uyumsuz çağrı geri döndüğünde, <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> belirteci çağırın.</span><span class="sxs-lookup"><span data-stu-id="6ceac-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="6ceac-212">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6ceac-212">In the following example:</span></span>

* <span data-ttu-id="6ceac-213">`await Task.Delay(5000, cts.Token);`uzun süreli zaman uyumsuz arka plan çalışmasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6ceac-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="6ceac-214">`BackgroundResourceMethod`yöntemi çağrılmadan önce atıldığı takdirde, uzun süre çalışan bir arka plan yöntemini temsil eder `Resource` .</span><span class="sxs-lookup"><span data-stu-id="6ceac-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
