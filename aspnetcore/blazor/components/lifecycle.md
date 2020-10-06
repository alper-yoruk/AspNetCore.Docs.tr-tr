---
title: ASP.NET Core Blazor yaşam döngüsü
author: guardrex
description: RazorASP.NET Core uygulamalarda bileşen yaşam döngüsü yöntemlerini nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: a43268acdb53bf811148fe795ef0434662ddb32f
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762233"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="5093a-103">ASP.NET Core Blazor yaşam döngüsü</span><span class="sxs-lookup"><span data-stu-id="5093a-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="5093a-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="5093a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5093a-105">BlazorÇerçeve, zaman uyumlu ve zaman uyumsuz yaşam döngüsü yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="5093a-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="5093a-106">Bileşen başlatma ve işleme sırasında bileşenlerde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="5093a-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="5093a-107">Aşağıdaki diyagramlarda Blazor yaşam döngüsü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="5093a-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="5093a-108">Yaşam döngüsü yöntemleri, bu makalenin aşağıdaki bölümlerinde örneklerle tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5093a-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="5093a-109">Bileşen yaşam döngüsü olayları:</span><span class="sxs-lookup"><span data-stu-id="5093a-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="5093a-110">Bileşen bir istek üzerinde ilk kez işleme alıyorsa:</span><span class="sxs-lookup"><span data-stu-id="5093a-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="5093a-111">Bileşenin örneğini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5093a-111">Create the component's instance.</span></span>
   * <span data-ttu-id="5093a-112">Özellik ekleme işlemini gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="5093a-112">Perform property injection.</span></span> <span data-ttu-id="5093a-113">Öğesini çalıştırın [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="5093a-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="5093a-114">Çağrısı yapın [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="5093a-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="5093a-115">Bir <xref:System.Threading.Tasks.Task> döndürülürse, geri <xref:System.Threading.Tasks.Task> beklemiştir ve bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="5093a-116">Bir <xref:System.Threading.Tasks.Task> döndürülmemişse, bileşeni işle.</span><span class="sxs-lookup"><span data-stu-id="5093a-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="5093a-117">Çağrısı yapın [`OnParametersSet{Async}`](#after-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="5093a-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set).</span></span> <span data-ttu-id="5093a-118">Bir <xref:System.Threading.Tasks.Task> döndürülürse, geri <xref:System.Threading.Tasks.Task> beklemiştir ve bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-118">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="5093a-119">Bir <xref:System.Threading.Tasks.Task> döndürülmemişse, bileşeni işle.</span><span class="sxs-lookup"><span data-stu-id="5093a-119">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>

![Bir::: No-Loc (Razor)::: bileşen::: No-Loc (Blazor)::: içinde bileşen yaşam döngüsü olayları](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="5093a-121">Belge Nesne Modeli (DOM) olay işleme:</span><span class="sxs-lookup"><span data-stu-id="5093a-121">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="5093a-122">Olay işleyicisi çalıştırıldı.</span><span class="sxs-lookup"><span data-stu-id="5093a-122">The event handler is run.</span></span>
1. <span data-ttu-id="5093a-123">Bir <xref:System.Threading.Tasks.Task> döndürülürse, geri <xref:System.Threading.Tasks.Task> beklemiştir ve bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-123">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="5093a-124">Bir <xref:System.Threading.Tasks.Task> döndürülürse, bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-124">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Belge Nesne Modeli (DOM) olay işleme](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="5093a-126">`Render`Yaşam döngüsü:</span><span class="sxs-lookup"><span data-stu-id="5093a-126">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="5093a-127">Bu bileşen ilk işleme değilse veya [`ShouldRender`](#suppress-ui-refreshing) olarak değerlendiriliyorsa `false` , bileşende başka işlemler yapmayın.</span><span class="sxs-lookup"><span data-stu-id="5093a-127">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="5093a-128">İşleme ağacı farkı (fark) oluşturun ve bileşeni oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5093a-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="5093a-129">DOM 'ı güncelleştirmek için Await.</span><span class="sxs-lookup"><span data-stu-id="5093a-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="5093a-130">Çağrısı yapın [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="5093a-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![İşleme yaşam döngüsü](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="5093a-132">Geliştirici [`StateHasChanged`](#state-changes) bir işleme yol açacak şekilde çağırır.</span><span class="sxs-lookup"><span data-stu-id="5093a-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="5093a-133">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="5093a-133">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="5093a-134">Parametreler ayarlanmadan önce</span><span class="sxs-lookup"><span data-stu-id="5093a-134">Before parameters are set</span></span>

<span data-ttu-id="5093a-135"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> işleme ağacındaki bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5093a-135"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="5093a-136"><xref:Microsoft.AspNetCore.Components.ParameterView> her çağrıldığında bileşen için parametre değerleri kümesini içerir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5093a-136"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="5093a-137">Varsayılan uygulama <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> , her bir özelliğin değerini [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) içinde karşılık gelen bir değere sahip veya özniteliğiyle ayarlar <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="5093a-137">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="5093a-138">İçinde karşılık gelen bir değere sahip olmayan parametreler <xref:Microsoft.AspNetCore.Components.ParameterView> değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-138">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="5093a-139">[`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)Çağrılmadıysa, özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-139">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="5093a-140">Örneğin, sınıftaki özelliklere gelen parametreleri atama gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="5093a-140">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="5093a-141">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="5093a-141">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5093a-142">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="5093a-142">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="5093a-143">Bileşen başlatma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="5093a-143">Component initialization methods</span></span>

<span data-ttu-id="5093a-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ve, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> bileşen başlangıç parametrelerini içindeki üst bileşeninden aldıktan sonra başlatıldığında çağrılır <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5093a-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="5093a-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Bileşen zaman uyumsuz bir işlem gerçekleştirdiğinde ve işlem tamamlandığında yenilenmesi gerektiğinde kullanın.</span><span class="sxs-lookup"><span data-stu-id="5093a-145">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="5093a-146">Zaman uyumlu bir işlem için, geçersiz kılın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="5093a-146">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="5093a-147">Zaman uyumsuz bir işlem gerçekleştirmek için <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> işlem üzerinde işlecini geçersiz kılın ve kullanın [`await`](/dotnet/csharp/language-reference/operators/await) :</span><span class="sxs-lookup"><span data-stu-id="5093a-147">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="5093a-148">Blazor Server[içerik](xref:blazor/fundamentals/additional-scenarios#render-mode) araması yapan uygulamalar <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_iki kez_** yapılır:</span><span class="sxs-lookup"><span data-stu-id="5093a-148">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="5093a-149">Bir kez, bileşen sayfanın bir parçası olarak başlangıçta statik olarak işlendiğinde.</span><span class="sxs-lookup"><span data-stu-id="5093a-149">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="5093a-150">Tarayıcı sunucuya geri bir bağlantı kurduğunda ikinci bir zaman.</span><span class="sxs-lookup"><span data-stu-id="5093a-150">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="5093a-151">İçindeki geliştirici kodunun <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> iki kez çalıştırılmasını engellemek için [prerendering sonrasında durum bilgisi olan yeniden bağlanma](#stateful-reconnection-after-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5093a-151">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="5093a-152">Bir uygulama prerendering olsa da, Blazor Server tarayıcıyla bir bağlantı kurulmadığı Için JavaScript 'e çağırma gibi bazı eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="5093a-152">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="5093a-153">Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-153">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="5093a-154">Daha fazla bilgi için bkz. [uygulamanın ne zaman prerendering](#detect-when-the-app-is-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5093a-154">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="5093a-155">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="5093a-155">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5093a-156">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="5093a-156">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="5093a-157">Parametreler ayarlandıktan sonra</span><span class="sxs-lookup"><span data-stu-id="5093a-157">After parameters are set</span></span>

<span data-ttu-id="5093a-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ya da <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> Şu şekilde adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="5093a-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="5093a-159">Veya içinde bileşen başlatıldıktan sonra <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5093a-159">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="5093a-160">Üst bileşen yeniden oluşturup şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="5093a-160">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="5093a-161">Yalnızca en az bir parametresinin değiştiği, bilinen temel sabit türler.</span><span class="sxs-lookup"><span data-stu-id="5093a-161">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="5093a-162">Tüm karmaşık türsüz parametreler.</span><span class="sxs-lookup"><span data-stu-id="5093a-162">Any complex-typed parameters.</span></span> <span data-ttu-id="5093a-163">Çerçeve, karmaşık yazılmış bir parametrenin değerlerinin dahili olarak değişip değişmediğini bilmez, bu yüzden parametre kümesini değiştirilmiş olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="5093a-163">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="5093a-164">Yaşam döngüsü olayında parametre ve özellik değerleri uygulanırken zaman uyumsuz iş olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5093a-164">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="5093a-165">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="5093a-165">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5093a-166">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="5093a-166">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="5093a-167">Bileşen oluşturulduktan sonra</span><span class="sxs-lookup"><span data-stu-id="5093a-167">After component render</span></span>

<span data-ttu-id="5093a-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> bir bileşen işlemeyi tamamladıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="5093a-169">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="5093a-169">Element and component references are populated at this point.</span></span> <span data-ttu-id="5093a-170">İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5093a-170">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="5093a-171">`firstRender`Ve için parametresi <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="5093a-171">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="5093a-172">, `true` Bileşen örneği ilk kez işlendiğinde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5093a-172">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="5093a-173">Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-173">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="5093a-174">Yaşam döngüsü olayında işleme hemen sonra zaman uyumsuz çalışma gerçekleşmelidir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5093a-174">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="5093a-175">Bir öğesinden döndürseniz bile <xref:System.Threading.Tasks.Task> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , çerçeve bu görev tamamlandıktan sonra bileşen için başka bir işleme çevrimi zamanlayamaz.</span><span class="sxs-lookup"><span data-stu-id="5093a-175">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="5093a-176">Bu, sonsuz bir işleme döngüsünden kaçınmaktır.</span><span class="sxs-lookup"><span data-stu-id="5093a-176">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="5093a-177">Diğer yaşam döngüsü yöntemlerinden farklı olduğundan, döndürülen görev tamamlandığında daha fazla işleme döngüsü zamanlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5093a-177">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="5093a-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *sunucudaki prerendering işlemi sırasında çağrılmaz*.</span><span class="sxs-lookup"><span data-stu-id="5093a-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="5093a-179">Yöntemler, prerendering bittikten sonra bileşen etkileşimli olarak işlendiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-179">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="5093a-180">Uygulama şu şekilde ön ekler:</span><span class="sxs-lookup"><span data-stu-id="5093a-180">When the app prerenders:</span></span>

1. <span data-ttu-id="5093a-181">Bileşeni, HTTP yanıtında bazı statik HTML biçimlendirmesi üretmek için sunucuda yürütülür.</span><span class="sxs-lookup"><span data-stu-id="5093a-181">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="5093a-182">Bu aşamada <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="5093a-182">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="5093a-183">`blazor.server.js`Tarayıcıda veya açıldığında `blazor.webassembly.js` , bileşen etkileşimli bir işleme modunda yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-183">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="5093a-184">Bir bileşen yeniden başlatıldıktan sonra, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** uygulama prerendering aşaması içinde olmadığından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-184">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="5093a-185">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="5093a-185">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5093a-186">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="5093a-186">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="5093a-187">UI yenilemeyi bastır</span><span class="sxs-lookup"><span data-stu-id="5093a-187">Suppress UI refreshing</span></span>

<span data-ttu-id="5093a-188"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>UI yenilemeyi gizlemek için geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="5093a-188">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="5093a-189">Uygulama döndürürse `true` , Kullanıcı arabirimi yenilenir:</span><span class="sxs-lookup"><span data-stu-id="5093a-189">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="5093a-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> bileşen her işlendiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="5093a-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-191">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="5093a-192">Daha fazla bilgi için bkz. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="5093a-192">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="5093a-193">Durum değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="5093a-193">State changes</span></span>

<span data-ttu-id="5093a-194"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşene durumunun değiştiğini bildirir.</span><span class="sxs-lookup"><span data-stu-id="5093a-194"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="5093a-195">Uygun olduğunda, çağırma <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden yönlendirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="5093a-195">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="5093a-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> yöntemler için otomatik olarak çağırılır <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="5093a-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="5093a-197">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="5093a-197">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="5093a-198">İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle</span><span class="sxs-lookup"><span data-stu-id="5093a-198">Handle incomplete async actions at render</span></span>

<span data-ttu-id="5093a-199">Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-199">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="5093a-200">`null`Yaşam döngüsü yöntemi yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-200">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="5093a-201">Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="5093a-201">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="5093a-202">Nesneler olduğunda yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme `null` .</span><span class="sxs-lookup"><span data-stu-id="5093a-202">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="5093a-203">`FetchData` Blazor Şablonların bileşeninde, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Asychronously () tahmin verileri almak için geçersiz kılınır `forecasts` .</span><span class="sxs-lookup"><span data-stu-id="5093a-203">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="5093a-204">Ne zaman olduğunda `forecasts` `null` , kullanıcıya bir yükleme iletisi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-204">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="5093a-205">`Task` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> İşlem tamamlandıktan sonra, bileşen güncelleştirilmiş duruma geri döner.</span><span class="sxs-lookup"><span data-stu-id="5093a-205">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="5093a-206">`Pages/FetchData.razor`Blazor Serverşablonda:</span><span class="sxs-lookup"><span data-stu-id="5093a-206">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="5093a-207">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="5093a-207">Handle errors</span></span>

<span data-ttu-id="5093a-208">Yaşam döngüsü yöntemi yürütme sırasında hataları işleme hakkında bilgi için bkz <xref:blazor/fundamentals/handle-errors#lifecycle-methods> ..</span><span class="sxs-lookup"><span data-stu-id="5093a-208">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="5093a-209">Prerendering sonrasında durum bilgisi olan yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="5093a-209">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="5093a-210">Bir Blazor Server uygulamada <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="5093a-210">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="5093a-211">Tarayıcı sunucuya geri bir bağlantı kurduğunda, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur.</span><span class="sxs-lookup"><span data-stu-id="5093a-211">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="5093a-212">[`OnInitialized{Async}`](#component-initialization-methods)Bileşeni başlatmak için yaşam döngüsü yöntemi mevcutsa, yöntemi *iki kez*yürütülür:</span><span class="sxs-lookup"><span data-stu-id="5093a-212">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="5093a-213">Bileşen statik olarak önceden kullanılırken.</span><span class="sxs-lookup"><span data-stu-id="5093a-213">When the component is prerendered statically.</span></span>
* <span data-ttu-id="5093a-214">Sunucu bağlantısı kurulduktan sonra.</span><span class="sxs-lookup"><span data-stu-id="5093a-214">After the server connection has been established.</span></span>

<span data-ttu-id="5093a-215">Bu, bileşen son işlendiğinde Kullanıcı arabiriminde görünen verilerde fark edilebilir bir değişikliğe neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-215">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="5093a-216">Bir uygulamadaki çift işleme senaryosunu önlemek için Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="5093a-216">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="5093a-217">Prerendering sırasında durumu önbelleğe almak için kullanılabilecek bir tanımlayıcı geçirin ve uygulamayı yeniden başlattıktan sonra durumu alma.</span><span class="sxs-lookup"><span data-stu-id="5093a-217">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="5093a-218">Bileşen durumunu kaydetmek için prerendering sırasında tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5093a-218">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="5093a-219">Önbelleğe alınan durumu almak için prerendering öğesinden sonra tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5093a-219">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="5093a-220">Aşağıdaki kod, bir `WeatherForecastService` şablon tabanlı Blazor Server uygulamada, Çift işlemeyi engelleyen güncelleştirilmiş bir güncelleme göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="5093a-220">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="5093a-221">Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> bkz <xref:blazor/fundamentals/additional-scenarios#render-mode> ..</span><span class="sxs-lookup"><span data-stu-id="5093a-221">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="5093a-222">Uygulamanın ne zaman prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="5093a-222">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="5093a-223">IDisposable ile bileşen atma</span><span class="sxs-lookup"><span data-stu-id="5093a-223">Component disposal with IDisposable</span></span>

<span data-ttu-id="5093a-224">Bir bileşen uygularsa <xref:System.IDisposable> , bileşen kullanıcı arabiriminden kaldırıldığında [ `Dispose` yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5093a-224">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="5093a-225">Bırakma işlemi, [Bileşen başlatma](#component-initialization-methods)sırasında da dahil olmak üzere herhangi bir zamanda gerçekleşebilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-225">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="5093a-226">Aşağıdaki bileşen `@implements IDisposable` ve `Dispose` yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="5093a-226">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="5093a-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>' In çağrılması `Dispose` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5093a-227">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="5093a-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> oluşturucuyu aşağı doğru olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmelerinin kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5093a-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="5093a-229">.NET etkinliklerinden olay işleyicilerini kaldırma.</span><span class="sxs-lookup"><span data-stu-id="5093a-229">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="5093a-230">Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, yönteminde bir olay işleyicisinin nasıl geri yükleneceğini göstermektedir `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="5093a-230">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="5093a-231">Özel alan ve lambda yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="5093a-231">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="5093a-232">Özel yöntem yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="5093a-232">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="5093a-233">İptal edilebilen arka plan çalışması</span><span class="sxs-lookup"><span data-stu-id="5093a-233">Cancelable background work</span></span>

<span data-ttu-id="5093a-234">Bileşenler genellikle ağ çağrıları yapma ( <xref:System.Net.Http.HttpClient> ) ve veritabanlarıyla etkileşim kurma gibi uzun süre çalışan arka plan işleri gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="5093a-234">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="5093a-235">Çeşitli durumlarda sistem kaynaklarını korumak için arka plan işinin durdurulması istenebilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-235">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="5093a-236">Örneğin, bir Kullanıcı bir bileşenden uzaklaştığında arka planda zaman uyumsuz işlemler otomatik olarak durdurulur.</span><span class="sxs-lookup"><span data-stu-id="5093a-236">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="5093a-237">Arka plan iş öğelerinin iptal etme gerektirmesinin diğer nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="5093a-237">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="5093a-238">Hatalı giriş verileriyle veya işleme parametreleriyle çalışan bir arka plan görevi başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="5093a-238">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="5093a-239">Çalışan arka plan iş öğelerinin geçerli kümesi, yeni bir iş öğesi kümesiyle değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5093a-239">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="5093a-240">Yürütülmekte olan görevlerin önceliği değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5093a-240">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="5093a-241">Uygulamanın sunucuya yeniden dağıtılması için kapatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5093a-241">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="5093a-242">Sunucu kaynakları sınırlı hale gelir, arka plan iş öğelerinin yeniden çizelgetasarımda.</span><span class="sxs-lookup"><span data-stu-id="5093a-242">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="5093a-243">Bir bileşene iptal edilebilen bir arka plan çalışma deseninin uygulanması için:</span><span class="sxs-lookup"><span data-stu-id="5093a-243">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="5093a-244">Ve kullanın <xref:System.Threading.CancellationTokenSource> <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="5093a-244">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="5093a-245">[Bileşenin elden çıkarılmasında](#component-disposal-with-idisposable) ve herhangi bir noktada iptal işlemi, belirteci el ile iptal ederek [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) istenir, arka plan işinin iptal edilmesi gerektiğini işaret edin.</span><span class="sxs-lookup"><span data-stu-id="5093a-245">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="5093a-246">Zaman uyumsuz çağrı geri döndüğünde, <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> belirteci çağırın.</span><span class="sxs-lookup"><span data-stu-id="5093a-246">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="5093a-247">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5093a-247">In the following example:</span></span>

* <span data-ttu-id="5093a-248">`await Task.Delay(5000, cts.Token);` uzun süreli zaman uyumsuz arka plan çalışmasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5093a-248">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="5093a-249">`BackgroundResourceMethod` yöntemi çağrılmadan önce atıldığı takdirde, uzun süre çalışan bir arka plan yöntemini temsil eder `Resource` .</span><span class="sxs-lookup"><span data-stu-id="5093a-249">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
