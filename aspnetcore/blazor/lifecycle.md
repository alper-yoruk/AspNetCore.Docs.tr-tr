---
title: ASP.NET Blazor Çekirdek yaşam döngüsü
author: guardrex
description: ASP.NET Core Blazor uygulamalarında Razor bileşen yaşam döngüsü yöntemlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791461"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="2b959-103">ASP.NET Blazor Çekirdek yaşam döngüsü</span><span class="sxs-lookup"><span data-stu-id="2b959-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="2b959-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2b959-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2b959-105">Çerçeve Blazor senkron ve eşzamanlı yaşam döngüsü yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="2b959-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="2b959-106">Bileşen başlatma ve işleme sırasında bileşenler üzerinde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="2b959-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="2b959-107">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="2b959-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="2b959-108">Bileşen başlatma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="2b959-108">Component initialization methods</span></span>

<span data-ttu-id="2b959-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> bileşen, ana bileşeninden ilk parametrelerini aldıktan sonra başlatıldığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2b959-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="2b959-110">Bileşen `OnInitializedAsync` bir eşzamanlı işlem gerçekleştirirken kullanın ve işlem tamamlandığında yenilemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="2b959-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="2b959-111">Senkron bir işlem için `OnInitialized`geçersiz kılma:</span><span class="sxs-lookup"><span data-stu-id="2b959-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="2b959-112">Eşzamanlı bir işlem gerçekleştirmek için, `OnInitializedAsync` işlemdeki `await` anahtar kelimeyi geçersiz kılın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="2b959-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="2b959-113">[İçeriklerini](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_iki kez_** önceden arayan sunucu uygulamaları:</span><span class="sxs-lookup"><span data-stu-id="2b959-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="2b959-114">Bileşen ilk olarak sayfanın bir parçası olarak statik olarak işlendiğinde bir kez.</span><span class="sxs-lookup"><span data-stu-id="2b959-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="2b959-115">Tarayıcı sunucuya bir bağlantı kurduğunda ikinci kez.</span><span class="sxs-lookup"><span data-stu-id="2b959-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="2b959-116">Geliştirici kodunun `OnInitializedAsync` iki kez çalışmasını önlemek için, ön işleme bölümünden [sonra Stateful yeniden bağlantısına](#stateful-reconnection-after-prerendering) bakın.</span><span class="sxs-lookup"><span data-stu-id="2b959-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="2b959-117">Sunucu Blazor uygulaması önceden işlenirken, tarayıcıyla bağlantı kurulmadığı ndan, JavaScript'e arama gibi belirli eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="2b959-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="2b959-118">Bileşenlerin önceden işlendiğinde farklı işlemesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2b959-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="2b959-119">Daha fazla bilgi için, uygulamanın ön oluşturma bölümü [ne zaman algıla'ya](#detect-when-the-app-is-prerendering) bakın.</span><span class="sxs-lookup"><span data-stu-id="2b959-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="2b959-120">Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır.</span><span class="sxs-lookup"><span data-stu-id="2b959-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2b959-121">Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2b959-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="2b959-122">Parametreler ayarlı önce</span><span class="sxs-lookup"><span data-stu-id="2b959-122">Before parameters are set</span></span>

<span data-ttu-id="2b959-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>oluşturma ağacında bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="2b959-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="2b959-124"><xref:Microsoft.AspNetCore.Components.ParameterView>her seferinde `SetParametersAsync` parametre değerlerinin tüm kümesini içerir.</span><span class="sxs-lookup"><span data-stu-id="2b959-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="2b959-125">Varsayılan uygulama, `SetParametersAsync` her özelliğin değerini, `[Parameter]` `[CascadingParameter]` `ParameterView`'de karşılık gelen bir değere sahip olan veya özniteliği olan' a göre ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2b959-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="2b959-126">Karşılık gelen değeri `ParameterView` olmayan parametreler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="2b959-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="2b959-127">`base.SetParametersAync` Çağrılmazsa, özel kod gelen parametrelerin değerini gereken şekilde yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2b959-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="2b959-128">Örneğin, gelen parametreleri sınıftaki özelliklere atama zorunluluğu yoktur.</span><span class="sxs-lookup"><span data-stu-id="2b959-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="2b959-129">Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır.</span><span class="sxs-lookup"><span data-stu-id="2b959-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2b959-130">Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2b959-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="2b959-131">Parametreler ayarlandıktan sonra</span><span class="sxs-lookup"><span data-stu-id="2b959-131">After parameters are set</span></span>

<span data-ttu-id="2b959-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> denir:</span><span class="sxs-lookup"><span data-stu-id="2b959-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="2b959-133">Bileşen baş harfe döndüğünde ve ilk parametre kümesini ana bileşeninden aldığında.</span><span class="sxs-lookup"><span data-stu-id="2b959-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="2b959-134">Ana bileşen yeniden işleyip sağladığında:</span><span class="sxs-lookup"><span data-stu-id="2b959-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="2b959-135">Yalnızca bilinen ilkel değişmez türleri en az bir parametre değişti.</span><span class="sxs-lookup"><span data-stu-id="2b959-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="2b959-136">Karmaşık daktio parametreler.</span><span class="sxs-lookup"><span data-stu-id="2b959-136">Any complex-typed parameters.</span></span> <span data-ttu-id="2b959-137">Çerçeve, karmaşık daktinolu parametredeğerlerinin dahili olarak mutasyona uğrayıp çözülmediğini bilemez, bu nedenle parametre kümesini değiştirilmiş olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="2b959-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="2b959-138">Parametreler ilerlerken eşzamanlı çalışma ve özellik değerleri `OnParametersSetAsync` yaşam döngüsü olayı sırasında oluşmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2b959-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="2b959-139">Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır.</span><span class="sxs-lookup"><span data-stu-id="2b959-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2b959-140">Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2b959-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="2b959-141">Bileşen işlemeden sonra</span><span class="sxs-lookup"><span data-stu-id="2b959-141">After component render</span></span>

<span data-ttu-id="2b959-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> bir bileşen işleme bittikten sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2b959-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="2b959-143">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="2b959-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="2b959-144">İşlenen DOM öğeleri üzerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenen içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2b959-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="2b959-145">Parametresi `firstRender` `OnAfterRenderAsync` için `OnAfterRender`ve:</span><span class="sxs-lookup"><span data-stu-id="2b959-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="2b959-146">Bileşen `true` örneğinin ilk kez görüntülenmidir.</span><span class="sxs-lookup"><span data-stu-id="2b959-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="2b959-147">Başlatma çalışmasının yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2b959-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="2b959-148">İşlemeden hemen sonra asenkron çalışma `OnAfterRenderAsync` yaşam döngüsü olayı sırasında gerçekleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="2b959-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="2b959-149">Bir <xref:System.Threading.Tasks.Task> geri `OnAfterRenderAsync`dönseniz bile, çerçeve, bu görev tamamlandıktan sonra bileşeniniz için başka bir işleme döngüsü zamanlamıyor.</span><span class="sxs-lookup"><span data-stu-id="2b959-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="2b959-150">Bu sonsuz bir render döngü önlemek içindir.</span><span class="sxs-lookup"><span data-stu-id="2b959-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="2b959-151">Döndürülen görev tamamlandıktan sonra başka bir işleme döngüsü zamanlayan diğer yaşam döngüsü yöntemlerinden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="2b959-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="2b959-152">`OnAfterRender``OnAfterRenderAsync` *ve sunucuda önişleme çağrıldığında.*</span><span class="sxs-lookup"><span data-stu-id="2b959-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="2b959-153">Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır.</span><span class="sxs-lookup"><span data-stu-id="2b959-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2b959-154">Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2b959-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="2b959-155">UI'yi yenilemeyi bastırma</span><span class="sxs-lookup"><span data-stu-id="2b959-155">Suppress UI refreshing</span></span>

<span data-ttu-id="2b959-156">UI <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> yenileme bastırmak için geçersiz kılma.</span><span class="sxs-lookup"><span data-stu-id="2b959-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="2b959-157">Uygulama dönerse, `true`UI yenilenir:</span><span class="sxs-lookup"><span data-stu-id="2b959-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="2b959-158">`ShouldRender`bileşen her işlendiher zaman denir.</span><span class="sxs-lookup"><span data-stu-id="2b959-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="2b959-159">Geçersiz `ShouldRender` kılınmış olsa bile, bileşen her zaman başlangıçta işlenir.</span><span class="sxs-lookup"><span data-stu-id="2b959-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="2b959-160">Durum değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="2b959-160">State changes</span></span>

<span data-ttu-id="2b959-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>durumunun değiştiğini fark eder.</span><span class="sxs-lookup"><span data-stu-id="2b959-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="2b959-162">Gerektiğinde, arama `StateHasChanged` bileşeniyeniden oluşturulabilir neden olur.</span><span class="sxs-lookup"><span data-stu-id="2b959-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="2b959-163">İşleme de tamamlanmamış async eylemleri işleme</span><span class="sxs-lookup"><span data-stu-id="2b959-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="2b959-164">Yaşam döngüsü olaylarında gerçekleştirilen eşsenkronize eylemler, bileşen oluşturulmadan önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="2b959-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="2b959-165">Nesneler, yaşam `null` döngüsü yöntemi yürütülerken verilerle dolu olabilir veya tam olarak doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="2b959-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="2b959-166">Nesnelerin baş harfe başlatOlduğunu doğrulamak için işleme mantığı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="2b959-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="2b959-167">Nesneler `null`.</span><span class="sxs-lookup"><span data-stu-id="2b959-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="2b959-168">Şablonların `FetchData` bileşeninde, `OnInitializedAsync` tahmin verilerini asychronously almak için geçersiz`forecasts`kılınan ( ). Blazor</span><span class="sxs-lookup"><span data-stu-id="2b959-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="2b959-169">Ne `forecasts` `null`zaman , bir yükleme iletisi kullanıcıya görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="2b959-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="2b959-170">Döndürülen `Task` tamamlandıktan `OnInitializedAsync` sonra, bileşen güncelleştirilmiş durumla yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="2b959-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="2b959-171">Sunucu şablonundaki Blazor *Sayfalar/FetchData.razor:*</span><span class="sxs-lookup"><span data-stu-id="2b959-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="2b959-172">IDisposable ile bileşen imhası</span><span class="sxs-lookup"><span data-stu-id="2b959-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="2b959-173">Bir bileşen uygularsa, <xref:System.IDisposable>bileşen UI'den kaldırıldığında Elden Çıkarma [yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2b959-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="2b959-174">Aşağıdaki bileşen `@implements IDisposable` kullanır `Dispose` ve yöntem:</span><span class="sxs-lookup"><span data-stu-id="2b959-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="2b959-175">Arama <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="2b959-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="2b959-176">`StateHasChanged`işleyiciyi yıkmanın bir parçası olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmeleri istenmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="2b959-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="2b959-177">.NET olaylarından olay işleyicilerinin aboneliğini iptal edin.</span><span class="sxs-lookup"><span data-stu-id="2b959-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="2b959-178">Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, yöntemde bir olay işleyicisinin nasıl çözülecek olduğunu `Dispose` gösterir:</span><span class="sxs-lookup"><span data-stu-id="2b959-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="2b959-179">Özel alan ve lambda yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="2b959-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="2b959-180">Özel yöntem yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="2b959-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="2b959-181">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="2b959-181">Handle errors</span></span>

<span data-ttu-id="2b959-182">Yaşam döngüsü yöntemi yürütme sırasında hataları <xref:blazor/handle-errors#lifecycle-methods>işleme hakkında bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="2b959-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="2b959-183">Önişleme den sonra durumlu yeniden bağlantı</span><span class="sxs-lookup"><span data-stu-id="2b959-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="2b959-184">Sunucu Blazor `RenderMode` `ServerPrerendered`uygulamasında, bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="2b959-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="2b959-185">Tarayıcı sunucuya bir bağlantı kurduktan sonra, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur.</span><span class="sxs-lookup"><span data-stu-id="2b959-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="2b959-186">Bileşeni başlatmaya yönelik [OnInitialized{Async}](#component-initialization-methods) yaşam döngüsü yöntemi varsa, yöntem *iki kez*yürütülür:</span><span class="sxs-lookup"><span data-stu-id="2b959-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="2b959-187">Bileşen statik olarak önceden işlendiğinde.</span><span class="sxs-lookup"><span data-stu-id="2b959-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="2b959-188">Sunucu bağlantısı kurulduktan sonra.</span><span class="sxs-lookup"><span data-stu-id="2b959-188">After the server connection has been established.</span></span>

<span data-ttu-id="2b959-189">Bu, bileşen sonunda işlendiğinde UI'de görüntülenen verilerde gözle görülür bir değişikliğe neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="2b959-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="2b959-190">Bir Blazor Server uygulamasında çift işleme senaryosunu önlemek için:</span><span class="sxs-lookup"><span data-stu-id="2b959-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="2b959-191">Ön işleme sırasında durumu önbelleğe almak ve uygulama yeniden başlatıldıktan sonra durumu almak için kullanılabilecek bir tanımlayıcıyı geçirin.</span><span class="sxs-lookup"><span data-stu-id="2b959-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="2b959-192">Bileşen durumunu kaydetmek için ön işleme sırasında tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2b959-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="2b959-193">Önbelleğe alınan durumu almak için ön işlemeden sonra tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2b959-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="2b959-194">Aşağıdaki kod, çift `WeatherForecastService` işlemeyi önleyen Blazor şablon tabanlı bir Sunucu uygulamasında güncelleştirilmiş bir kod gösterir:</span><span class="sxs-lookup"><span data-stu-id="2b959-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="2b959-195">Hakkında daha fazla `RenderMode`bilgi <xref:blazor/hosting-model-configuration#render-mode>için bkz.</span><span class="sxs-lookup"><span data-stu-id="2b959-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="2b959-196">Uygulamanın ne zaman önceden oluşturulaca-son veriş olduğunu algılama</span><span class="sxs-lookup"><span data-stu-id="2b959-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
