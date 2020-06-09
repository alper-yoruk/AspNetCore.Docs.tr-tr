---
title: ASP.NET Core Blazor webassembly performansı en iyi yöntemleri
author: pranavkm
description: ASP.NET Core Blazor weelsembly uygulamalarında performansı artırmaya yönelik ipuçları ve sık karşılaşılan performans sorunlarını önleme.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 950d87a6f09e998e47e96c93c5d68bb3f19ddafb
ms.sourcegitcommit: 74d80a36103fdbd54baba0118535a4647f511913
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84529638"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="84e84-103">ASP.NET Core Blazor webassembly performansı en iyi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="84e84-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="84e84-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="84e84-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="84e84-105">Bu makalede, Blazor weelsembly performans en iyi uygulamaları ASP.NET Core için yönergeler sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="84e84-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="84e84-106">Gereksiz bileşen işlediğini önleyin</span><span class="sxs-lookup"><span data-stu-id="84e84-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="84e84-107">, bir bileşenin, bileşenin değiştirmediğinden rerendering bir bileşeni dağıtma algoritması bir bileşeni engeller.</span><span class="sxs-lookup"><span data-stu-id="84e84-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="84e84-108"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>Bileşen işleme üzerinde ayrıntılı denetim için geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="84e84-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="84e84-109">İlk işleme sonrasında hiçbir şekilde hiçbir değişiklik olmayan bir UI bileşeni yazıyorsanız, şunu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> döndürecek şekilde yapılandırın `false` :</span><span class="sxs-lookup"><span data-stu-id="84e84-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="84e84-110">Çoğu uygulama ayrıntılı denetim gerektirmez, ancak bir <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Kullanıcı arabirimi olayına yanıt veren bir bileşeni seçmeli olarak işlemek için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84e84-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="84e84-111">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="84e84-111">In the following example:</span></span>

* <span data-ttu-id="84e84-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>geçersiz kılınır ve <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Bu, bileşen yüklendiğinde başlangıçta olan değerine ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="84e84-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="84e84-113">Düğme seçildiğinde, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> olarak ayarlanır ve `true` bileşen güncelleştirilmiş ile yeniden başlamaya zorlar `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="84e84-113">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="84e84-114">Rerendering hemen sonra, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` düğmenin bir sonraki seçilene kadar daha fazla rerendering 'yi engellemek için, geri değerini olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84e84-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="84e84-115">Daha fazla bilgi için bkz. <xref:blazor/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="84e84-115">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="84e84-116">Yeniden kullanılabilir parçaları sanallaştırın</span><span class="sxs-lookup"><span data-stu-id="84e84-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="84e84-117">Bileşenler, kod ve biçimlendirme için yeniden kullanılabilir parçalar üretmede kullanışlı bir yaklaşım sunar.</span><span class="sxs-lookup"><span data-stu-id="84e84-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="84e84-118">Genel olarak, uygulamanın gereksinimleriyle en iyi şekilde uyum sağlayan ayrı bileşenleri yazmayı öneririz.</span><span class="sxs-lookup"><span data-stu-id="84e84-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="84e84-119">Bir desteklenmediği uyarısıyla, her ek alt bileşen bir üst bileşeni işlemek için gereken toplam süreye katkıda bulunur.</span><span class="sxs-lookup"><span data-stu-id="84e84-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="84e84-120">Çoğu uygulama için ek yük gözardı edilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="84e84-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="84e84-121">Çok sayıda bileşen üreten uygulamalar, işlenen bileşenlerin sayısını sınırlama gibi işlem yükünü azaltmak için stratejileri kullanmayı göz önünde bulundurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84e84-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="84e84-122">Örneğin, bileşenleri içeren yüzlerce satırı işleyen bir kılavuz veya liste, işlemcinin işlenmesi için yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="84e84-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="84e84-123">Belirli bir zamanda bileşenlerin yalnızca bir alt kümesinin işlenmesi için bir kılavuz veya liste düzeninin sanallaştırılmasını düşünün.</span><span class="sxs-lookup"><span data-stu-id="84e84-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="84e84-124">Bileşen alt kümesi işleme örneği için, sanallaştırma örnek uygulamasında aşağıdaki bileşenlere bakın [(ASPNET/Samples GitHub deposu)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="84e84-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="84e84-125">`Virtualize`bileşen ([Shared/virtualrender. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): <xref:Microsoft.AspNetCore.Components.ComponentBase> Kullanıcı kaydırması temelinde bir hava durumu veri satırını Işlemek için uygulayan C# dilinde yazılmış bir bileşen.</span><span class="sxs-lookup"><span data-stu-id="84e84-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="84e84-126">`FetchData`bileşen ([Pages/fetchdata. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): tek seferde `Virtualize` 25 ' lik Hava durumu verilerini göstermek için bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="84e84-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="84e84-127">Verileri sıralamak için JavaScript birlikte çalışabiliri önleyin</span><span class="sxs-lookup"><span data-stu-id="84e84-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="84e84-128">BlazorWebassembly 'de, bir JavaScript (js) birlikte çalışma çağrısının webassembly-js sınırını geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="84e84-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="84e84-129">İki bağlamda içeriğin serileştirilmesi ve serisini kaldırma, uygulama için işleme yükünü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="84e84-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="84e84-130">Sık kullanılan JS birlikte çalışma çağrıları performansı olumsuz etkiler.</span><span class="sxs-lookup"><span data-stu-id="84e84-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="84e84-131">Verilerin sınır genelinde sıralanmasını azaltmak için, uygulamanın birçok küçük yükü tek bir büyük yük içinde birleştirip birleştireceğini, WebAssembly ve JS arasında yüksek hacimli bağlam geçişi yapmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="84e84-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="84e84-132">System. Text. JSON kullanın</span><span class="sxs-lookup"><span data-stu-id="84e84-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="84e84-133">' nin JS birlikte çalışma uygulamasının <xref:System.Text.Json> , düşük bellek ayırması olan yüksek performanslı BIR JSON serileştirme kitaplığı olan ' i kullanır.</span><span class="sxs-lookup"><span data-stu-id="84e84-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="84e84-134">Kullanmak <xref:System.Text.Json> , bir veya daha fazla ALTERNATIF JSON kitaplığı ekleyerek ek uygulama yükü boyutuna neden olmaz.</span><span class="sxs-lookup"><span data-stu-id="84e84-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="84e84-135">Geçiş Kılavuzu için, bkz. [Newtonsoft. JSON 'Dan System. Text. JSON 'a geçiş](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="84e84-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="84e84-136">Uygun yerlerde eşzamanlı ve unmarshalled JS birlikte çalışma API 'Lerini kullanın</span><span class="sxs-lookup"><span data-stu-id="84e84-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="84e84-137">WebAssembly <xref:Microsoft.JSInterop.IJSRuntime> , sunucu uygulamaları için kullanılabilen tek sürümün iki ek sürümünü sunmaktadır Blazor :</span><span class="sxs-lookup"><span data-stu-id="84e84-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="84e84-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime>, zaman uyumsuz sürümlerden daha az ek yük olan, JS birlikte çalışabilirlik çağrılarının zaman uyumlu olarak yüklenmesine izin verir:</span><span class="sxs-lookup"><span data-stu-id="84e84-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="84e84-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>sıradan olmayan JS birlikte çalışabilirlik çağrılarına izin verir:</span><span class="sxs-lookup"><span data-stu-id="84e84-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="84e84-140">Kullanırken <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> , JS birlikte çalışma yaklaşımının en az ek yükü olsa da, bu API 'lerle etkileşimde bulunmak için gereken JavaScript API 'leri Şu anda açıklanmamıştır ve gelecekteki sürümlerde yapılan değişikliklere tabidir.</span><span class="sxs-lookup"><span data-stu-id="84e84-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="84e84-141">Uygulama boyutunu azalt</span><span class="sxs-lookup"><span data-stu-id="84e84-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="84e84-142">Ara dil (IL) bağlama</span><span class="sxs-lookup"><span data-stu-id="84e84-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="84e84-143">[Bağlama Blazor WebAssembly uygulaması](xref:host-and-deploy/blazor/configure-linker) , uygulamanın ikili dosyalarında kullanılmayan kodu kırparak uygulamanın boyutunu azaltır.</span><span class="sxs-lookup"><span data-stu-id="84e84-143">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="84e84-144">Varsayılan olarak, bağlayıcı yalnızca yapılandırmada oluşturulurken etkin olur `Release` .</span><span class="sxs-lookup"><span data-stu-id="84e84-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="84e84-145">Bu özellikten yararlanmak için, [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu kullanarak uygulamayı dağıtım için yayımlayın `Release` :</span><span class="sxs-lookup"><span data-stu-id="84e84-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="84e84-146">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="84e84-146">Compression</span></span>

<span data-ttu-id="84e84-147">Bir Blazor weelsembly uygulaması yayımlandığında, çıkış sırasında, uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="84e84-147">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="84e84-148">, içerik eklemek için sunucuyu kullanır ve statik olarak sıkıştırılan dosyaları sunar.</span><span class="sxs-lookup"><span data-stu-id="84e84-148"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="84e84-149">Bir uygulama dağıtıldıktan sonra uygulamanın sıkıştırılmış dosyalara hizmet ettiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="84e84-149">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="84e84-150">Tarayıcının Geliştirici Araçları ağ sekmesini inceleyin ve dosyaların veya ile birlikte sunulduğunu doğrulayın `Content-Encoding: br` `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="84e84-150">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="84e84-151">Ana bilgisayar sıkıştırılmış dosyalara hizmet vermemişse, içindeki yönergeleri izleyin <xref:host-and-deploy/blazor/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="84e84-151">If the host isn't serving compressed files, follow the instructions in <xref:host-and-deploy/blazor/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="84e84-152">Kullanılmayan özellikleri devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="84e84-152">Disable unused features</span></span>

Blazor<span data-ttu-id="84e84-153">WebAssembly çalışma zamanı, uygulamanın daha küçük bir yük boyutu için gerektirmemesi durumunda devre dışı bırakılabilen aşağıdaki .NET özelliklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="84e84-153"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="84e84-154">Saat dilimi bilgilerinin doğru olması için bir veri dosyası dahildir.</span><span class="sxs-lookup"><span data-stu-id="84e84-154">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="84e84-155">Uygulama bu özelliği gerektirmiyorsa, `BlazorEnableTimeZoneSupport` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :</span><span class="sxs-lookup"><span data-stu-id="84e84-155">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="84e84-156">Doğru çalışma gibi API 'Leri oluşturmak için harmanlama bilgileri eklenmiştir <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="84e84-156">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="84e84-157">Uygulamanın harmanlama verileri gerektirmediğinden emin değilseniz, `BlazorWebAssemblyPreserveCollationData` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :</span><span class="sxs-lookup"><span data-stu-id="84e84-157">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
