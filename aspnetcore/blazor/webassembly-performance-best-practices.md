---
title: ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri
author: pranavkm
description: ASP.NET Core uygulamalarında performansı artırma Blazor WebAssembly ve sık karşılaşılan performans sorunlarından kaçınmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 91d0eb7b4910d1cf19b179372546afa63cd3f9c1
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009602"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="6ba2c-103">ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="6ba2c-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="6ba2c-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="6ba2c-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="6ba2c-105">Bu makalede ASP.NET Core Blazor WebAssembly performans en iyi uygulamalarına yönelik yönergeler sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="6ba2c-106">Gereksiz bileşen işlediğini önleyin</span><span class="sxs-lookup"><span data-stu-id="6ba2c-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="6ba2c-107">Blazor, bir bileşenin, bileşenin değiştirmediğinden rerendering bir bileşeni dağıtma algoritması bir bileşeni engeller.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="6ba2c-108"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>Bileşen işleme üzerinde ayrıntılı denetim için geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="6ba2c-109">İlk işleme sonrasında hiçbir şekilde hiçbir değişiklik olmayan bir UI bileşeni yazıyorsanız, şunu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> döndürecek şekilde yapılandırın `false` :</span><span class="sxs-lookup"><span data-stu-id="6ba2c-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="6ba2c-110">Çoğu uygulama ayrıntılı denetim gerektirmez, ancak bir <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Kullanıcı arabirimi olayına yanıt veren bir bileşeni seçmeli olarak işlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="6ba2c-111">Kullanmak <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> çok sayıda bileşenin işlendiği senaryolarda de önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="6ba2c-112">Kılavuzdaki kılavuz <xref:Microsoft.AspNetCore.Components.EventCallback> çağrılarının bir hücresinde tek bir bileşende kullanıldığı bir kılavuz düşünün <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="6ba2c-113">Çağırma <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , her alt bileşenin yeniden işlenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="6ba2c-114">Yalnızca az sayıda hücre rerendering gerektiriyorsa, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> gereksiz sıra performans cezasından kaçınmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="6ba2c-115">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6ba2c-115">In the following example:</span></span>

* <span data-ttu-id="6ba2c-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> geçersiz kılınır ve <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Bu, bileşen yüklendiğinde başlangıçta olan değerine ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="6ba2c-117">Düğme seçildiğinde, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> olarak ayarlanır ve `true` bileşen güncelleştirilmiş ile yeniden başlamaya zorlar `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="6ba2c-118">Rerendering hemen sonra, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` düğmenin bir sonraki seçilene kadar daha fazla rerendering 'yi engellemek için, geri değerini olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="6ba2c-119">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="6ba2c-120">Yeniden kullanılabilir parçaları sanallaştırın</span><span class="sxs-lookup"><span data-stu-id="6ba2c-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="6ba2c-121">Bileşenler, kod ve biçimlendirme için yeniden kullanılabilir parçalar üretmede kullanışlı bir yaklaşım sunar.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="6ba2c-122">Genel olarak, uygulamanın gereksinimleriyle en iyi şekilde uyum sağlayan ayrı bileşenleri yazmayı öneririz.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="6ba2c-123">Bir desteklenmediği uyarısıyla, her ek alt bileşen bir üst bileşeni işlemek için gereken toplam süreye katkıda bulunur.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="6ba2c-124">Çoğu uygulama için ek yük gözardı edilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="6ba2c-125">Çok sayıda bileşen üreten uygulamalar, işlenen bileşenlerin sayısını sınırlama gibi işlem yükünü azaltmak için stratejileri kullanmayı göz önünde bulundurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="6ba2c-126">Örneğin, bileşenleri içeren yüzlerce satırı işleyen bir kılavuz veya liste, işlemcinin işlenmesi için yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="6ba2c-127">Belirli bir zamanda bileşenlerin yalnızca bir alt kümesinin işlenmesi için bir kılavuz veya liste düzeninin sanallaştırılmasını düşünün.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="6ba2c-128">Bileşen alt kümesi işleme örneği için örnek uygulamada aşağıdaki bileşenlere bakın [ `Virtualization` (ASPNET/Samples GitHub deposu)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="6ba2c-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="6ba2c-129">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): <xref:Microsoft.AspNetCore.Components.ComponentBase> Kullanıcı kaydırması temelinde bir hava durumu veri satırı kümesini işlemek Için uygulayan C# dilinde yazılmış bir bileşen.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="6ba2c-130">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): tek seferde `Virtualize` 25 ' lik Hava durumu verilerini göstermek için bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="6ba2c-131">Verileri sıralamak için JavaScript birlikte çalışabiliri önleyin</span><span class="sxs-lookup"><span data-stu-id="6ba2c-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="6ba2c-132">Blazor WebAssembly' De, bir JavaScript (js) birlikte çalışma çağrısının WebAssembly-js sınırını geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="6ba2c-133">İki bağlamda içeriğin serileştirilmesi ve serisini kaldırma, uygulama için işleme yükünü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="6ba2c-134">Sık kullanılan JS birlikte çalışma çağrıları performansı olumsuz etkiler.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="6ba2c-135">Verilerin sınır genelinde sıralanmasını azaltmak için, uygulamanın birçok küçük yükü tek bir büyük yük içinde birleştirip birleştireceğini, WebAssembly ve JS arasında yüksek hacimli bağlam geçişi yapmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="6ba2c-136">Üzerinde System.Text.Jskullan</span><span class="sxs-lookup"><span data-stu-id="6ba2c-136">Use System.Text.Json</span></span>

<span data-ttu-id="6ba2c-137">Blazor' nin JS birlikte çalışma uygulamasının <xref:System.Text.Json> , düşük bellek ayırması olan yüksek performanslı BIR JSON serileştirme kitaplığı olan ' i kullanır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="6ba2c-138">Kullanmak <xref:System.Text.Json> , bir veya daha fazla ALTERNATIF JSON kitaplığı ekleyerek ek uygulama yükü boyutuna neden olmaz.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="6ba2c-139">Geçiş Kılavuzu için, bkz. ' [den `Newtonsoft.Json` `System.Text.Json` ' ye geçiş ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="6ba2c-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="6ba2c-140">Uygun yerlerde eşzamanlı ve unmarshalled JS birlikte çalışma API 'Lerini kullanın</span><span class="sxs-lookup"><span data-stu-id="6ba2c-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="6ba2c-141">Blazor WebAssembly , <xref:Microsoft.JSInterop.IJSRuntime> uygulamalar için kullanılabilen tek sürümün iki ek sürümünü sunar Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="6ba2c-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="6ba2c-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> , zaman uyumsuz sürümlerden daha az ek yük olan, JS birlikte çalışabilirlik çağrılarının zaman uyumlu olarak yüklenmesine izin verir:</span><span class="sxs-lookup"><span data-stu-id="6ba2c-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="6ba2c-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> sıradan olmayan JS birlikte çalışabilirlik çağrılarına izin verir:</span><span class="sxs-lookup"><span data-stu-id="6ba2c-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="6ba2c-144">Kullanırken <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> , JS birlikte çalışma yaklaşımının en az ek yükü olsa da, bu API 'lerle etkileşimde bulunmak için gereken JavaScript API 'leri Şu anda açıklanmamıştır ve gelecekteki sürümlerde yapılan değişikliklere tabidir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="6ba2c-145">Uygulama boyutunu azalt</span><span class="sxs-lookup"><span data-stu-id="6ba2c-145">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="6ba2c-146">Ara dil (IL) bağlama</span><span class="sxs-lookup"><span data-stu-id="6ba2c-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="6ba2c-147">Uygulamanın [bağlanması Blazor WebAssembly ](xref:blazor/host-and-deploy/configure-linker) , uygulamanın ikili dosyalarında kullanılmayan kodu kırparak uygulamanın boyutunu azaltır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="6ba2c-148">Varsayılan olarak, bağlayıcı yalnızca yapılandırmada oluşturulurken etkin olur `Release` .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-148">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="6ba2c-149">Bu özellikten yararlanmak için, [`dotnet publish`](/dotnet/core/tools/dotnet-publish) [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan komutunu kullanarak uygulamayı dağıtım için yayımlayın `Release` :</span><span class="sxs-lookup"><span data-stu-id="6ba2c-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="6ba2c-150">Yavaş yük derlemeleri</span><span class="sxs-lookup"><span data-stu-id="6ba2c-150">Lazy load assemblies</span></span>

<span data-ttu-id="6ba2c-151">Derlemeler bir rota için gerektiğinde çalışma zamanında derlemeleri yükleyin.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="6ba2c-152">Daha fazla bilgi için bkz. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="6ba2c-153">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="6ba2c-153">Compression</span></span>

<span data-ttu-id="6ba2c-154">Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="6ba2c-155">Blazor , içerik eklemek için sunucuyu kullanır ve statik olarak sıkıştırılan dosyaları sunar.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="6ba2c-156">Bir uygulama dağıtıldıktan sonra uygulamanın sıkıştırılmış dosyalara hizmet ettiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="6ba2c-157">Tarayıcının Geliştirici Araçları ağ sekmesini inceleyin ve dosyaların veya ile birlikte sunulduğunu doğrulayın `Content-Encoding: br` `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="6ba2c-158">Ana bilgisayar sıkıştırılmış dosyalara hizmet vermemişse, içindeki yönergeleri izleyin <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="6ba2c-159">Kullanılmayan özellikleri devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="6ba2c-159">Disable unused features</span></span>

<span data-ttu-id="6ba2c-160">Blazor WebAssemblyçalışma zamanı, uygulamanın daha küçük bir yük boyutu için gerektirmemesi durumunda devre dışı bırakılabilen aşağıdaki .NET özelliklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="6ba2c-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="6ba2c-161">Saat dilimi bilgilerinin doğru olması için bir veri dosyası dahildir.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="6ba2c-162">Uygulama bu özelliği gerektirmiyorsa, `BlazorEnableTimeZoneSupport` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :</span><span class="sxs-lookup"><span data-stu-id="6ba2c-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="6ba2c-163">Varsayılan olarak, Blazor WebAssembly Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken Genelleştirme kaynaklarını taşır.</span><span class="sxs-lookup"><span data-stu-id="6ba2c-163">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="6ba2c-164">Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan sabit kültürü destekleyecek şekilde yapılandırabilirsiniz `en-US` :</span><span class="sxs-lookup"><span data-stu-id="6ba2c-164">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```
::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="6ba2c-165">Doğru çalışma gibi API 'Leri oluşturmak için harmanlama bilgileri eklenmiştir <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6ba2c-165">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="6ba2c-166">Uygulamanın harmanlama verileri gerektirmediğinden emin değilseniz, `BlazorWebAssemblyPreserveCollationData` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :</span><span class="sxs-lookup"><span data-stu-id="6ba2c-166">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
