---
title: ASP.NET Core Blazor yaşam döngüsü
author: guardrex
description: RazorASP.NET Core uygulamalarda bileşen yaşam döngüsü yöntemlerini nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: acaa276efda9fb4d09a5c1b1ca59c6abde1b64ec
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252402"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="49ec1-103">ASP.NET Core Blazor yaşam döngüsü</span><span class="sxs-lookup"><span data-stu-id="49ec1-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="49ec1-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="49ec1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="49ec1-105">BlazorÇerçeve, zaman uyumlu ve zaman uyumsuz yaşam döngüsü yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="49ec1-106">Bileşen başlatma ve işleme sırasında bileşenlerde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="49ec1-107">Aşağıdaki diyagramlarda Blazor yaşam döngüsü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="49ec1-108">Yaşam döngüsü yöntemleri, bu makalenin aşağıdaki bölümlerinde örneklerle tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="49ec1-109">Bileşen yaşam döngüsü olayları:</span><span class="sxs-lookup"><span data-stu-id="49ec1-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="49ec1-110">Bileşen bir istek üzerinde ilk kez işleme alıyorsa:</span><span class="sxs-lookup"><span data-stu-id="49ec1-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="49ec1-111">Bileşenin örneğini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="49ec1-111">Create the component's instance.</span></span>
   * <span data-ttu-id="49ec1-112">Özellik ekleme işlemini gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="49ec1-112">Perform property injection.</span></span> <span data-ttu-id="49ec1-113">Öğesini çalıştırın [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="49ec1-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="49ec1-114">Çağrısı yapın [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="49ec1-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="49ec1-115">Bir <xref:System.Threading.Tasks.Task> döndürülürse,, <xref:System.Threading.Tasks.Task> bekletildi ve bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="49ec1-116">Bir <xref:System.Threading.Tasks.Task> döndürülürse, bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="49ec1-117">[`OnParametersSet{Async}`](#after-parameters-are-set)Bileşeni çağırın ve işleme.</span><span class="sxs-lookup"><span data-stu-id="49ec1-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="49ec1-118">Bir <xref:System.Threading.Tasks.Task> ' dan döndürülürse, `OnParametersSetAsync` ' <xref:System.Threading.Tasks.Task> de beklemiş olur ve sonra bileşen tekrar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Bir::: No-Loc (Razor)::: bileşen::: No-Loc (Blazor)::: içinde bileşen yaşam döngüsü olayları](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="49ec1-120">Belge Nesne Modeli (DOM) olay işleme:</span><span class="sxs-lookup"><span data-stu-id="49ec1-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="49ec1-121">Olay işleyicisi çalıştırıldı.</span><span class="sxs-lookup"><span data-stu-id="49ec1-121">The event handler is run.</span></span>
1. <span data-ttu-id="49ec1-122">Bir <xref:System.Threading.Tasks.Task> döndürülürse, geri <xref:System.Threading.Tasks.Task> beklemiştir ve bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="49ec1-123">Bir <xref:System.Threading.Tasks.Task> döndürülürse, bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Belge Nesne Modeli (DOM) olay işleme](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="49ec1-125">`Render`Yaşam döngüsü:</span><span class="sxs-lookup"><span data-stu-id="49ec1-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="49ec1-126">Bileşende daha fazla işleme işlemi yapmaktan kaçının:</span><span class="sxs-lookup"><span data-stu-id="49ec1-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="49ec1-127">İlk işleme sonra.</span><span class="sxs-lookup"><span data-stu-id="49ec1-127">After the first render.</span></span>
   * <span data-ttu-id="49ec1-128">Ne [`ShouldRender`](#suppress-ui-refreshing) zaman `false` .</span><span class="sxs-lookup"><span data-stu-id="49ec1-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="49ec1-129">İşleme ağacı farkı (fark) oluşturun ve bileşeni oluşturun.</span><span class="sxs-lookup"><span data-stu-id="49ec1-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="49ec1-130">DOM 'ı güncelleştirmek için Await.</span><span class="sxs-lookup"><span data-stu-id="49ec1-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="49ec1-131">Çağrısı yapın [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="49ec1-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![İşleme yaşam döngüsü](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="49ec1-133">Geliştirici [`StateHasChanged`](#state-changes) bir işleme yol açacak şekilde çağırır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="49ec1-134">Daha fazla bilgi için bkz. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="49ec1-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="49ec1-135">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="49ec1-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="49ec1-136">Parametreler ayarlanmadan önce</span><span class="sxs-lookup"><span data-stu-id="49ec1-136">Before parameters are set</span></span>

<span data-ttu-id="49ec1-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> işleme ağacındaki bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="49ec1-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="49ec1-138"><xref:Microsoft.AspNetCore.Components.ParameterView> her çağrıldığında bileşen için parametre değerleri kümesini içerir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-138"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="49ec1-139">Varsayılan uygulama <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> , her bir özelliğin değerini [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) içinde karşılık gelen bir değere sahip veya özniteliğiyle ayarlar <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-139">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="49ec1-140">İçinde karşılık gelen bir değere sahip olmayan parametreler <xref:Microsoft.AspNetCore.Components.ParameterView> değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-140">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="49ec1-141">[`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)Çağrılmadıysa, özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-141">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="49ec1-142">Örneğin, sınıftaki özelliklere gelen parametreleri atama gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="49ec1-142">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="49ec1-143">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-143">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49ec1-144">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="49ec1-144">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="49ec1-145">Bileşen başlatma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="49ec1-145">Component initialization methods</span></span>

<span data-ttu-id="49ec1-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ve, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> bileşen başlangıç parametrelerini içindeki üst bileşeninden aldıktan sonra başlatıldığında çağrılır <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="49ec1-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Bileşen zaman uyumsuz bir işlem gerçekleştirdiğinde ve işlem tamamlandığında yenilenmesi gerektiğinde kullanın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-147">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="49ec1-148">Zaman uyumlu bir işlem için, geçersiz kılın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="49ec1-148">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="49ec1-149">Zaman uyumsuz bir işlem gerçekleştirmek için <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> işlem üzerinde işlecini geçersiz kılın ve kullanın [`await`](/dotnet/csharp/language-reference/operators/await) :</span><span class="sxs-lookup"><span data-stu-id="49ec1-149">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="49ec1-150">Blazor Server[içerik](xref:blazor/fundamentals/additional-scenarios#render-mode) araması yapan uygulamalar <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *iki kez* yapılır:</span><span class="sxs-lookup"><span data-stu-id="49ec1-150">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="49ec1-151">Bir kez, bileşen sayfanın bir parçası olarak başlangıçta statik olarak işlendiğinde.</span><span class="sxs-lookup"><span data-stu-id="49ec1-151">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="49ec1-152">Tarayıcı sunucuya geri bir bağlantı kurduğunda ikinci bir zaman.</span><span class="sxs-lookup"><span data-stu-id="49ec1-152">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="49ec1-153">İçindeki geliştirici kodunun <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> iki kez çalıştırılmasını engellemek için [prerendering sonrasında durum bilgisi olan yeniden bağlanma](#stateful-reconnection-after-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-153">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="49ec1-154">Bir uygulama prerendering olsa da, Blazor Server tarayıcıyla bir bağlantı kurulmadığı Için JavaScript 'e çağırma gibi bazı eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-154">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="49ec1-155">Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-155">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="49ec1-156">Daha fazla bilgi için bkz. [uygulamanın ne zaman prerendering](#detect-when-the-app-is-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-156">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="49ec1-157">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-157">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49ec1-158">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="49ec1-158">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="49ec1-159">Parametreler ayarlandıktan sonra</span><span class="sxs-lookup"><span data-stu-id="49ec1-159">After parameters are set</span></span>

<span data-ttu-id="49ec1-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ya da <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> Şu şekilde adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="49ec1-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="49ec1-161">Veya içinde bileşen başlatıldıktan sonra <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-161">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="49ec1-162">Üst bileşen yeniden oluşturup şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="49ec1-162">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="49ec1-163">Yalnızca en az bir parametresinin değiştiği, bilinen temel sabit türler.</span><span class="sxs-lookup"><span data-stu-id="49ec1-163">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="49ec1-164">Tüm karmaşık türsüz parametreler.</span><span class="sxs-lookup"><span data-stu-id="49ec1-164">Any complex-typed parameters.</span></span> <span data-ttu-id="49ec1-165">Çerçeve, karmaşık yazılmış bir parametrenin değerlerinin dahili olarak değişip değişmediğini bilmez, bu yüzden parametre kümesini değiştirilmiş olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-165">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="49ec1-166">Yaşam döngüsü olayında parametre ve özellik değerleri uygulanırken zaman uyumsuz iş olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-166">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="49ec1-167">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-167">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49ec1-168">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="49ec1-168">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="49ec1-169">Bileşen oluşturulduktan sonra</span><span class="sxs-lookup"><span data-stu-id="49ec1-169">After component render</span></span>

<span data-ttu-id="49ec1-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> bir bileşen işlemeyi tamamladıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="49ec1-171">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="49ec1-171">Element and component references are populated at this point.</span></span> <span data-ttu-id="49ec1-172">İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-172">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="49ec1-173">`firstRender`Ve için parametresi <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="49ec1-173">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="49ec1-174">, `true` Bileşen örneği ilk kez işlendiğinde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-174">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="49ec1-175">Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-175">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="49ec1-176">Yaşam döngüsü olayında işleme hemen sonra zaman uyumsuz çalışma gerçekleşmelidir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-176">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="49ec1-177">Bir öğesinden döndürseniz bile <xref:System.Threading.Tasks.Task> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , çerçeve bu görev tamamlandıktan sonra bileşen için başka bir işleme çevrimi zamanlayamaz.</span><span class="sxs-lookup"><span data-stu-id="49ec1-177">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="49ec1-178">Bu, sonsuz bir işleme döngüsünden kaçınmaktır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-178">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="49ec1-179">Diğer yaşam döngüsü yöntemlerinden farklı olduğundan, döndürülen görev tamamlandığında daha fazla işleme döngüsü zamanlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="49ec1-179">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="49ec1-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *sunucudaki prerendering işlemi sırasında çağrılmaz*.</span><span class="sxs-lookup"><span data-stu-id="49ec1-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="49ec1-181">Yöntemler, prerendering bittikten sonra bileşen etkileşimli olarak işlendiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-181">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="49ec1-182">Uygulama şu şekilde ön ekler:</span><span class="sxs-lookup"><span data-stu-id="49ec1-182">When the app prerenders:</span></span>

1. <span data-ttu-id="49ec1-183">Bileşeni, HTTP yanıtında bazı statik HTML biçimlendirmesi üretmek için sunucuda yürütülür.</span><span class="sxs-lookup"><span data-stu-id="49ec1-183">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="49ec1-184">Bu aşamada <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="49ec1-184">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="49ec1-185">`blazor.server.js`Tarayıcıda veya açıldığında `blazor.webassembly.js` , bileşen etkileşimli bir işleme modunda yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-185">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="49ec1-186">Bir bileşen yeniden başlatıldıktan sonra, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>  uygulama prerendering aşaması içinde olmadığından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-186">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="49ec1-187">Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-187">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49ec1-188">Daha fazla bilgi için bkz. [bileşen aktiften çıkarma `IDisposable` ](#component-disposal-with-idisposable) bölümü.</span><span class="sxs-lookup"><span data-stu-id="49ec1-188">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="49ec1-189">UI yenilemeyi bastır</span><span class="sxs-lookup"><span data-stu-id="49ec1-189">Suppress UI refreshing</span></span>

<span data-ttu-id="49ec1-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>UI yenilemeyi gizlemek için geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-190">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="49ec1-191">Uygulama döndürürse `true` , Kullanıcı arabirimi yenilenir:</span><span class="sxs-lookup"><span data-stu-id="49ec1-191">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="49ec1-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> bileşen her işlendiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="49ec1-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-193">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="49ec1-194">Daha fazla bilgi için bkz. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="49ec1-194">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="49ec1-195">Durum değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="49ec1-195">State changes</span></span>

<span data-ttu-id="49ec1-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşene durumunun değiştiğini bildirir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="49ec1-197">Uygun olduğunda, çağırma <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden yönlendirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="49ec1-197">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="49ec1-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> yöntemler için otomatik olarak çağırılır <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="49ec1-199">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="49ec1-199">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="49ec1-200">Daha fazla bilgi için bkz. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="49ec1-200">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="49ec1-201">İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle</span><span class="sxs-lookup"><span data-stu-id="49ec1-201">Handle incomplete async actions at render</span></span>

<span data-ttu-id="49ec1-202">Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-202">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="49ec1-203">`null`Yaşam döngüsü yöntemi yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-203">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="49ec1-204">Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-204">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="49ec1-205">Nesneler olduğunda yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme `null` .</span><span class="sxs-lookup"><span data-stu-id="49ec1-205">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="49ec1-206">`FetchData` Blazor Şablonların bileşeninde, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zaman uyumsuz olarak tahmin verileri () almak için geçersiz kılınır `forecasts` .</span><span class="sxs-lookup"><span data-stu-id="49ec1-206">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="49ec1-207">Ne zaman olduğunda `forecasts` `null` , kullanıcıya bir yükleme iletisi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-207">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="49ec1-208">`Task` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> İşlem tamamlandıktan sonra, bileşen güncelleştirilmiş duruma geri döner.</span><span class="sxs-lookup"><span data-stu-id="49ec1-208">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="49ec1-209">`Pages/FetchData.razor`Blazor Serverşablonda:</span><span class="sxs-lookup"><span data-stu-id="49ec1-209">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="49ec1-210">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="49ec1-210">Handle errors</span></span>

<span data-ttu-id="49ec1-211">Yaşam döngüsü yöntemi yürütme sırasında hataları işleme hakkında bilgi için bkz <xref:blazor/fundamentals/handle-errors#lifecycle-methods> ..</span><span class="sxs-lookup"><span data-stu-id="49ec1-211">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="49ec1-212">Prerendering sonrasında durum bilgisi olan yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="49ec1-212">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="49ec1-213">Bir Blazor Server uygulamada <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-213">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="49ec1-214">Tarayıcı sunucuya geri bir bağlantı kurduğunda, bileşen *yeniden* işlenir ve bileşen artık etkileşimli olur.</span><span class="sxs-lookup"><span data-stu-id="49ec1-214">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="49ec1-215">[`OnInitialized{Async}`](#component-initialization-methods)Bileşeni başlatmak için yaşam döngüsü yöntemi mevcutsa, yöntemi *iki kez* yürütülür:</span><span class="sxs-lookup"><span data-stu-id="49ec1-215">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="49ec1-216">Bileşen statik olarak önceden kullanılırken.</span><span class="sxs-lookup"><span data-stu-id="49ec1-216">When the component is prerendered statically.</span></span>
* <span data-ttu-id="49ec1-217">Sunucu bağlantısı kurulduktan sonra.</span><span class="sxs-lookup"><span data-stu-id="49ec1-217">After the server connection has been established.</span></span>

<span data-ttu-id="49ec1-218">Bu, bileşen son işlendiğinde Kullanıcı arabiriminde görünen verilerde fark edilebilir bir değişikliğe neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-218">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="49ec1-219">Bir uygulamadaki çift işleme senaryosunu önlemek için Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="49ec1-219">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="49ec1-220">Prerendering sırasında durumu önbelleğe almak için kullanılabilecek bir tanımlayıcı geçirin ve uygulamayı yeniden başlattıktan sonra durumu alma.</span><span class="sxs-lookup"><span data-stu-id="49ec1-220">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="49ec1-221">Bileşen durumunu kaydetmek için prerendering sırasında tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-221">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="49ec1-222">Önbelleğe alınan durumu almak için prerendering öğesinden sonra tanımlayıcıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-222">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="49ec1-223">Aşağıdaki kod, bir `WeatherForecastService` şablon tabanlı Blazor Server uygulamada, Çift işlemeyi engelleyen güncelleştirilmiş bir güncelleme göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="49ec1-223">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="49ec1-224">Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> bkz <xref:blazor/fundamentals/additional-scenarios#render-mode> ..</span><span class="sxs-lookup"><span data-stu-id="49ec1-224">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="49ec1-225">Uygulamanın ne zaman prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="49ec1-225">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="49ec1-226">IDisposable ile bileşen atma</span><span class="sxs-lookup"><span data-stu-id="49ec1-226">Component disposal with IDisposable</span></span>

<span data-ttu-id="49ec1-227">Bir bileşen uygularsa <xref:System.IDisposable> , bileşen kullanıcı arabiriminden kaldırıldığında [ `Dispose` yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağrılır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-227">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="49ec1-228">Bırakma işlemi, [Bileşen başlatma](#component-initialization-methods)sırasında da dahil olmak üzere herhangi bir zamanda gerçekleşebilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-228">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="49ec1-229">Aşağıdaki bileşen `@implements IDisposable` ve `Dispose` yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="49ec1-229">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="49ec1-230"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>' In çağrılması `Dispose` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="49ec1-230">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="49ec1-231"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> oluşturucuyu aşağı doğru olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmelerinin kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="49ec1-231"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="49ec1-232">.NET etkinliklerinden olay işleyicilerini kaldırma.</span><span class="sxs-lookup"><span data-stu-id="49ec1-232">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="49ec1-233">Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, yönteminde bir olay işleyicisinin nasıl geri yükleneceğini göstermektedir `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="49ec1-233">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="49ec1-234">Özel alan ve lambda yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="49ec1-234">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="49ec1-235">Özel yöntem yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="49ec1-235">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="49ec1-236">İptal edilebilen arka plan çalışması</span><span class="sxs-lookup"><span data-stu-id="49ec1-236">Cancelable background work</span></span>

<span data-ttu-id="49ec1-237">Bileşenler genellikle ağ çağrıları yapma ( <xref:System.Net.Http.HttpClient> ) ve veritabanlarıyla etkileşim kurma gibi uzun süre çalışan arka plan işleri gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-237">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="49ec1-238">Çeşitli durumlarda sistem kaynaklarını korumak için arka plan işinin durdurulması istenebilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-238">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="49ec1-239">Örneğin, bir Kullanıcı bir bileşenden uzaklaştığında arka planda zaman uyumsuz işlemler otomatik olarak durdurulur.</span><span class="sxs-lookup"><span data-stu-id="49ec1-239">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="49ec1-240">Arka plan iş öğelerinin iptal etme gerektirmesinin diğer nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="49ec1-240">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="49ec1-241">Hatalı giriş verileriyle veya işleme parametreleriyle çalışan bir arka plan görevi başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="49ec1-241">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="49ec1-242">Çalışan arka plan iş öğelerinin geçerli kümesi, yeni bir iş öğesi kümesiyle değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-242">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="49ec1-243">Yürütülmekte olan görevlerin önceliği değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-243">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="49ec1-244">Uygulamanın sunucuya yeniden dağıtılması için kapatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="49ec1-244">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="49ec1-245">Sunucu kaynakları sınırlı hale gelir, arka plan iş öğelerinin yeniden çizelgetasarımda.</span><span class="sxs-lookup"><span data-stu-id="49ec1-245">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="49ec1-246">Bir bileşene iptal edilebilen bir arka plan çalışma deseninin uygulanması için:</span><span class="sxs-lookup"><span data-stu-id="49ec1-246">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="49ec1-247">Ve kullanın <xref:System.Threading.CancellationTokenSource> <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="49ec1-247">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="49ec1-248">[Bileşenin elden çıkarılmasında](#component-disposal-with-idisposable) ve herhangi bir noktada iptal işlemi, belirteci el ile iptal ederek [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) istenir, arka plan işinin iptal edilmesi gerektiğini işaret edin.</span><span class="sxs-lookup"><span data-stu-id="49ec1-248">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="49ec1-249">Zaman uyumsuz çağrı geri döndüğünde, <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> belirteci çağırın.</span><span class="sxs-lookup"><span data-stu-id="49ec1-249">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="49ec1-250">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="49ec1-250">In the following example:</span></span>

* <span data-ttu-id="49ec1-251">`await Task.Delay(5000, cts.Token);` uzun süreli zaman uyumsuz arka plan çalışmasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="49ec1-251">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="49ec1-252">`BackgroundResourceMethod` yöntemi çağrılmadan önce atıldığı takdirde, uzun süre çalışan bir arka plan yöntemini temsil eder `Resource` .</span><span class="sxs-lookup"><span data-stu-id="49ec1-252">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="49ec1-253">Blazor Server yeniden bağlanma olayları</span><span class="sxs-lookup"><span data-stu-id="49ec1-253">Blazor Server reconnection events</span></span>

<span data-ttu-id="49ec1-254">Bu makalede ele alınan bileşen yaşam döngüsü olayları, yeniden [ Blazor Server bağlanma olay işleyicilerinden](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui)ayrı olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="49ec1-254">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="49ec1-255">Bir Blazor Server uygulama SignalR istemciyle bağlantısını kaybettiğinde yalnızca Kullanıcı Arabirimi güncelleştirmeleri kesintiye uğrar.</span><span class="sxs-lookup"><span data-stu-id="49ec1-255">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="49ec1-256">Bağlantı yeniden oluşturulduğunda Kullanıcı Arabirimi güncelleştirmeleri sürdürülür.</span><span class="sxs-lookup"><span data-stu-id="49ec1-256">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="49ec1-257">Devre işleyici olayları ve yapılandırması hakkında daha fazla bilgi için bkz <xref:blazor/fundamentals/additional-scenarios> ..</span><span class="sxs-lookup"><span data-stu-id="49ec1-257">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
