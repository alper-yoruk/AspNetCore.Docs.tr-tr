---
title: "ASP.NET Core 'de geç yük derlemeleri Blazor WebAssembly"
author: guardrex
description: 'ASP.NET Core uygulamalarında derlemelerin nasıl yavaş yükleneceğini öğrenin Blazor WebAssembly .'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6a1feffb5341d432d6d1949a9e26b9537b85ba03
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054794"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="abe22-103">ASP.NET Core 'de geç yük derlemeleri Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="abe22-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="abe22-104">By [Safia Abdalla](https://safia.rocks) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="abe22-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="abe22-105">Blazor WebAssembly Uygulama başlatma performansı, bazı uygulama derlemelerinin yüklenmesi gerekene kadar artırılabilir ve bu da *yavaş yükleme* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="abe22-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading* .</span></span> <span data-ttu-id="abe22-106">Örneğin, yalnızca tek bir bileşeni işlemek için kullanılan derlemeler yalnızca Kullanıcı o bileşene gittiğinde yüklenecek şekilde ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="abe22-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="abe22-107">Yüklendikten sonra derlemeler istemci tarafında önbelleğe alınır ve gelecekteki tüm gezinmeler için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="abe22-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="abe22-108">Blazoryavaş yükleme özelliği, Kullanıcı belirli bir rotaya geçtiğinde çalışma zamanı sırasında derlemeleri yükleyen yavaş yükleme için uygulama derlemelerini işaretlemenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="abe22-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="abe22-109">Özelliği proje dosyasındaki değişikliklerden ve uygulamanın yönlendiricisinde yapılan değişikliklerle oluşur.</span><span class="sxs-lookup"><span data-stu-id="abe22-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="abe22-110">Blazor ServerDerlemeler bir uygulamadaki istemciye indirilmediğinden derleme yavaş yüklemesi uygulama avantajına sahip değildir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="abe22-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="abe22-111">Proje dosyası</span><span class="sxs-lookup"><span data-stu-id="abe22-111">Project file</span></span>

<span data-ttu-id="abe22-112">Öğeyi kullanarak uygulamanın proje dosyasında () yavaş yükleme için derlemeleri işaretleyin `.csproj` `BlazorWebAssemblyLazyLoad` .</span><span class="sxs-lookup"><span data-stu-id="abe22-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="abe22-113">Uzantı ile derleme adını kullanın `.dll` .</span><span class="sxs-lookup"><span data-stu-id="abe22-113">Use the assembly name with the `.dll` extension.</span></span> <span data-ttu-id="abe22-114">BlazorFramework, bu öğe grubu tarafından belirtilen derlemelerin uygulama başlatma sırasında yüklenmesine engel olur.</span><span class="sxs-lookup"><span data-stu-id="abe22-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="abe22-115">Aşağıdaki örnek, yavaş yükleme için büyük bir özel derlemeyi ( `GrantImaharaRobotControls.dll` ) işaretler.</span><span class="sxs-lookup"><span data-stu-id="abe22-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="abe22-116">Yavaş yükleme için işaretlenen bir derlemenin bağımlılıkları varsa, proje dosyasında da yavaş yükleme için işaretlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="abe22-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="abe22-117">`Router` bileşeni</span><span class="sxs-lookup"><span data-stu-id="abe22-117">`Router` component</span></span>

<span data-ttu-id="abe22-118">Blazor`Router`bileşeni, hangi derlemelerin Blazor yönlendirilebilir bileşenleri arayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="abe22-118">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="abe22-119">`Router`Bileşen ayrıca, kullanıcının gittiği yol için bileşenin işlenmesinden de sorumludur.</span><span class="sxs-lookup"><span data-stu-id="abe22-119">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="abe22-120">`Router`Bileşeni, `OnNavigateAsync` yavaş yükleme ile birlikte kullanılabilecek bir özelliği destekler.</span><span class="sxs-lookup"><span data-stu-id="abe22-120">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="abe22-121">Uygulamanın `Router` bileşeninde ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="abe22-121">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="abe22-122">Bir `OnNavigateAsync` geri çağırma ekleyin.</span><span class="sxs-lookup"><span data-stu-id="abe22-122">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="abe22-123">`OnNavigateAsync`İşleyici, Kullanıcı şunları yaparken çağrılır:</span><span class="sxs-lookup"><span data-stu-id="abe22-123">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="abe22-124">Doğrudan tarayıcısından gezinerek bir yolu ilk kez ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="abe22-124">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="abe22-125">Bağlantı veya çağırma kullanarak yeni bir yola gider <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="abe22-125">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="abe22-126">Geç yüklenmiş derlemeler yönlendirilebilir bileşenler içeriyorsa, bileşene bir [liste](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (örneğin, adlandırılmış `lazyLoadedAssemblies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="abe22-126">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="abe22-127">Derlemeler <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> yönlendirilebilir bileşenleri içermesi durumunda derlemeler koleksiyona geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="abe22-127">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="abe22-128">Çerçeve, derlemeleri rotalar için arar ve yeni yollar bulunursa rota koleksiyonunu günceller.</span><span class="sxs-lookup"><span data-stu-id="abe22-128">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="abe22-129">`OnNavigateAsync`Geri çağırma işlenmeyen bir özel durum oluşturursa, [ Blazor hata Kullanıcı arabirimi](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) çağrılır.</span><span class="sxs-lookup"><span data-stu-id="abe22-129">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="abe22-130">İçindeki derleme yükleme mantığı `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="abe22-130">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="abe22-131">`OnNavigateAsync``NavigationContext`, hedef yol ( `Path` ) ve iptal belirteci () dahil olmak üzere geçerli zaman uyumsuz gezinti olayı hakkında bilgi sağlayan bir parametreye sahiptir `CancellationToken` :</span><span class="sxs-lookup"><span data-stu-id="abe22-131">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="abe22-132">`Path`Özelliği, kullanıcının, gibi uygulamanın temel yoluna göre hedef yoludur `/robot` .</span><span class="sxs-lookup"><span data-stu-id="abe22-132">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="abe22-133">, `CancellationToken` Zaman uyumsuz görevin iptalini gözlemlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="abe22-133">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="abe22-134">`OnNavigateAsync` Kullanıcı farklı bir sayfaya gittiğinde, çalışmakta olan gezinti görevini otomatik olarak iptal eder.</span><span class="sxs-lookup"><span data-stu-id="abe22-134">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="abe22-135">İçinde `OnNavigateAsync` , yüklenecek derlemeleri belirleme mantığını uygulayın.</span><span class="sxs-lookup"><span data-stu-id="abe22-135">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="abe22-136">Seçeneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="abe22-136">Options include:</span></span>

* <span data-ttu-id="abe22-137">Yöntemin içindeki koşullu denetimler `OnNavigateAsync` .</span><span class="sxs-lookup"><span data-stu-id="abe22-137">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="abe22-138">Bileşene eklenen ya da blok içinde uygulanan bir arama tablosu [`@code`](xref:mvc/views/razor#code) .</span><span class="sxs-lookup"><span data-stu-id="abe22-138">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="abe22-139">`LazyAssemblyLoader` , derlemeleri yüklemek için çerçeve tarafından sunulan tek bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="abe22-139">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="abe22-140">`LazyAssemblyLoader` `Router` Bileşene ekle:</span><span class="sxs-lookup"><span data-stu-id="abe22-140">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="abe22-141">, `LazyAssemblyLoader` Aşağıdakileri sağlayan `LoadAssembliesAsync` yöntemini sağlar:</span><span class="sxs-lookup"><span data-stu-id="abe22-141">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="abe22-142">Bir ağ çağrısı aracılığıyla derlemeleri getirmek için JS birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="abe22-142">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="abe22-143">Derlemeleri tarayıcıda WebAssembly üzerinde yürüten çalışma zamanına yükler.</span><span class="sxs-lookup"><span data-stu-id="abe22-143">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="abe22-144">Framework 'ün yavaş yükleme uygulamasıyla, barındırılan bir çözümde prerendering ile geç yükleme desteklenir Blazor .</span><span class="sxs-lookup"><span data-stu-id="abe22-144">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted Blazor solution.</span></span> <span data-ttu-id="abe22-145">Prerendering sırasında, yavaş yükleme için işaretlenenler de dahil olmak üzere tüm derlemelerin yüklendiği varsayılır.</span><span class="sxs-lookup"><span data-stu-id="abe22-145">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="abe22-146">`LazyAssemblyLoader` *Sunucu* projesinin `Startup.ConfigureServices` yöntemine () el ile kaydolun `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="abe22-146">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="abe22-147">İçerikle Kullanıcı etkileşimi `<Navigating>`</span><span class="sxs-lookup"><span data-stu-id="abe22-147">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="abe22-148">Birkaç saniye sürebilen derlemeler yüklenirken, `Router` bileşen kullanıcıya bir sayfa geçişinin oluştuğunu gösterebilir:</span><span class="sxs-lookup"><span data-stu-id="abe22-148">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="abe22-149">[`@using`](xref:mvc/views/razor#using)Ad alanı için bir yönerge ekleyin <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="abe22-149">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="abe22-150">`<Navigating>`Sayfa geçişi olayları sırasında görüntülenecek işaretlerle bileşene bir etiket ekleyin.</span><span class="sxs-lookup"><span data-stu-id="abe22-150">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="abe22-151">Üzerinde iptalleri işle `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="abe22-151">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="abe22-152">`NavigationContext`Geri çağırmaya geçirilen nesne, `OnNavigateAsync` `CancellationToken` Yeni bir gezinti olayı gerçekleştiğinde ayarlanmış bir içerir.</span><span class="sxs-lookup"><span data-stu-id="abe22-152">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="abe22-153">`OnNavigateAsync`Bu iptal belirteci, `OnNavigateAsync` geri çağırma işlemini eski bir gezinmede çalıştırmaya devam etmeden kaçınmak üzere ayarlandığında, geri çağırma işlemi throw olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="abe22-153">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="abe22-154">Bir kullanıcı yönlendirme A 'ya gider ve sonra B 'ye yönlendirmek için, uygulama `OnNavigateAsync` a yolu için geri çağırma çalıştırmaya devam edememelidir:</span><span class="sxs-lookup"><span data-stu-id="abe22-154">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="abe22-155">' Deki iptal belirteci `NavigationContext` iptal edildiğinde, bir bileşeni önceki bir gezinmede işlemek gibi istenmeden davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="abe22-155">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="abe22-156">`OnNavigateAsync` olaylar ve yeniden adlandırılmış derleme dosyaları</span><span class="sxs-lookup"><span data-stu-id="abe22-156">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="abe22-157">Kaynak yükleyicisi, dosyasında tanımlanan derleme adlarını kullanır `blazor.boot.json` .</span><span class="sxs-lookup"><span data-stu-id="abe22-157">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="abe22-158">[Derlemeler yeniden adlandırılırsa](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), yöntemlerde kullanılan derleme adları `OnNavigateAsync` ve dosyadaki derleme adları `blazor.boot.json` eşitlenmemiş.</span><span class="sxs-lookup"><span data-stu-id="abe22-158">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="abe22-159">Bunu düzeltmek için:</span><span class="sxs-lookup"><span data-stu-id="abe22-159">To rectify this:</span></span>

* <span data-ttu-id="abe22-160">Kullanılacak derleme adlarını belirlerken uygulamanın üretim ortamında çalışıp çalışmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="abe22-160">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="abe22-161">Yeniden adlandırılmış derleme adlarını ayrı bir dosyada depolayın ve bu dosyadan okuyarak, ve yöntemlerinde hangi derleme adının kullanılacağını saptayın `LazyLoadAssemblyService` `OnNavigateAsync` .</span><span class="sxs-lookup"><span data-stu-id="abe22-161">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="abe22-162">Örnek Tamam</span><span class="sxs-lookup"><span data-stu-id="abe22-162">Complete example</span></span>

<span data-ttu-id="abe22-163">Aşağıdaki tamamlanmış `Router` Bileşen, `GrantImaharaRobotControls.dll` Kullanıcı öğesine gittiğinde derlemeyi yüklemeyi gösterir `/robot` .</span><span class="sxs-lookup"><span data-stu-id="abe22-163">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="abe22-164">Sayfa geçişleri sırasında, kullanıcıya biçimlendirilmiş bir ileti görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="abe22-164">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="abe22-165">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="abe22-165">Troubleshoot</span></span>

* <span data-ttu-id="abe22-166">Beklenmeyen işleme gerçekleşirse (örneğin, önceki bir gezinmede bir bileşen işlendiğinde), iptal belirteci ayarlanmışsa kodun bulunduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="abe22-166">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="abe22-167">Uygulama başlangıcında derlemeler hala yüklenirse, derlemenin proje dosyasında yavaş yüklenmiş olarak işaretlendiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="abe22-167">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="abe22-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="abe22-168">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
