---
title: 'ASP.NET Core :::no-loc(Blazor)::: Gelişmiş senaryolar'
author: guardrex
description: "' Deki gelişmiş senaryolar :::no-loc(Blazor)::: , El Ile RenderTreeBuilder mantığını bir uygulamaya nasıl dahil leyeceğinizi öğrenin."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056562"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="19048-103">ASP.NET Core :::no-loc(Blazor)::: Gelişmiş senaryolar</span><span class="sxs-lookup"><span data-stu-id="19048-103">ASP.NET Core :::no-loc(Blazor)::: advanced scenarios</span></span>

<span data-ttu-id="19048-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="19048-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="19048-105">:::no-loc(Blazor Server)::: devre işleyici</span><span class="sxs-lookup"><span data-stu-id="19048-105">:::no-loc(Blazor Server)::: circuit handler</span></span>

<span data-ttu-id="19048-106">:::no-loc(Blazor Server)::: kodun bir Kullanıcı devresi durumunda değişiklikler üzerinde kod çalıştırmaya izin veren bir *devhandler* tanımlamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="19048-106">:::no-loc(Blazor Server)::: allows code to define a *circuit handler* , which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="19048-107">Devre işleyici, `CircuitHandler` uygulamanın hizmet kapsayıcısındaki sınıfından türeterek ve kayıt işleminden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="19048-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="19048-108">Bir devre işleyicinin aşağıdaki örneği açık :::no-loc(SignalR)::: bağlantıları izler:</span><span class="sxs-lookup"><span data-stu-id="19048-108">The following example of a circuit handler tracks open :::no-loc(SignalR)::: connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="19048-109">Devre işleyicileri DI kullanılarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="19048-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="19048-110">Kapsamlı örnekler, bir devrenin örneği başına oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="19048-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="19048-111">`TrackingCircuitHandler`Önceki örnekte kullanılarak, tüm devrelerin durumu izlenmelidir çünkü bir tek hizmet oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="19048-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="19048-112">Özel bir devre işleyicisindeki Yöntemler işlenmeyen bir özel durum oluşturuyorsam, özel durum devre için önemli olur :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="19048-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="19048-113">Bir işleyicinin kodundaki veya yöntemleri çağrılan özel durumlara tolerans sağlamak için kodu bir veya daha fazla [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) deyimde hata işleme ve günlüğe kaydetme ile sarın.</span><span class="sxs-lookup"><span data-stu-id="19048-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="19048-114">Bir kullanıcının bağlantısı kesilmediği ve Framework devre durumunu temizlemede bir devre dışı bırakıldığında, çerçeve devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="19048-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="19048-115">Kapsamı elden atılırken, uygulayan hiçbir devre kapsamlı dı hizmeti yok <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="19048-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="19048-116">Herhangi bir DI hizmeti, elden çıkarma sırasında işlenmeyen bir özel durum oluşturursa, çerçeve özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="19048-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="19048-117">El ile RenderTreeBuilder mantığı</span><span class="sxs-lookup"><span data-stu-id="19048-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="19048-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> bileşenleri ve öğeleri düzenlemek için yöntemler sağlar. bu sayede bileşenleri C# kodunda el ile oluşturma da dahil olmak üzere.</span><span class="sxs-lookup"><span data-stu-id="19048-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="19048-119"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Bileşenlerini oluşturmak için kullanımı gelişmiş bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="19048-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="19048-120">Hatalı biçimlendirilmiş bir bileşen (örneğin, kapatılmamış bir biçimlendirme etiketi) tanımsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="19048-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="19048-121">Aşağıdaki `PetDetails` bileşeni, başka bir bileşende el ile yerleşik olarak kullanılabilecek şekilde göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="19048-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="19048-122">Aşağıdaki örnekte, `CreateComponent` yöntemindeki döngü üç `PetDetails` bileşen oluşturur.</span><span class="sxs-lookup"><span data-stu-id="19048-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="19048-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Sıra numarası olan yöntemlerde, sıra numaraları kaynak kodu satır numaralarıdır.</span><span class="sxs-lookup"><span data-stu-id="19048-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="19048-124">:::no-loc(Blazor):::Fark algoritması, ayrı çağrı etkinleştirmeleri değil ayrı kod satırlarına karşılık gelen sıra numaralarına dayanır.</span><span class="sxs-lookup"><span data-stu-id="19048-124">The :::no-loc(Blazor)::: difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="19048-125">Yöntemler içeren bir bileşen oluştururken <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , dizi numaralarına yönelik bağımsız değişkenleri kod olarak kodlayın.</span><span class="sxs-lookup"><span data-stu-id="19048-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="19048-126">**Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak kötü performansa neden olabilir.**</span><span class="sxs-lookup"><span data-stu-id="19048-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="19048-127">Daha fazla bilgi için bkz. [kod satırı numaralarıyla Ilgili sıra numaraları ve yürütme sırası çalışmıyor](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) bölümü.</span><span class="sxs-lookup"><span data-stu-id="19048-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="19048-128">`BuiltContent` bileşeninde</span><span class="sxs-lookup"><span data-stu-id="19048-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="19048-129">İçindeki türler, <xref:Microsoft.AspNetCore.Components.RenderTree> işleme işlemlerinin *sonuçlarının* işlenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="19048-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="19048-130">Bunlar, çerçeve uygulamasının iç ayrıntılardır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="19048-130">These are internal details of the :::no-loc(Blazor)::: framework implementation.</span></span> <span data-ttu-id="19048-131">Bu türlerin *dengesizleşilmesi* ve gelecekteki sürümlerde değişikliğe tabi olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="19048-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="19048-132">Sıra numaraları, kod satırı numaralarıyla ilgilidir ve yürütme sırası değildir</span><span class="sxs-lookup"><span data-stu-id="19048-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="19048-133">:::no-loc(Razor)::: bileşen dosyaları ( `.razor` ) her zaman derlenir.</span><span class="sxs-lookup"><span data-stu-id="19048-133">:::no-loc(Razor)::: component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="19048-134">Derleme adımı, çalışma zamanında uygulama performansını geliştiren bilgileri eklemek için kullanılabilir olduğundan, kod yorumlanması üzerinde olası bir avantajdır.</span><span class="sxs-lookup"><span data-stu-id="19048-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="19048-135">Bu geliştirmelerin önemli bir örneği, *sıra numaraları* içerir.</span><span class="sxs-lookup"><span data-stu-id="19048-135">A key example of these improvements involves *sequence numbers* .</span></span> <span data-ttu-id="19048-136">Sıra numaraları, hangi çıkışların ayrı ve sıralı kod satırlarından geldiğini çalışma zamanına işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="19048-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="19048-137">Çalışma zamanı, doğrusal bir zamanda, genel ağaç farkı algoritması için genellikle mümkün olandan çok daha hızlı olan etkili ağaç SLA 'ları oluşturmak için bu bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="19048-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="19048-138">Aşağıdaki :::no-loc(Razor)::: bileşen () dosyasını göz önünde bulundurun `.razor` :</span><span class="sxs-lookup"><span data-stu-id="19048-138">Consider the following :::no-loc(Razor)::: component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="19048-139">Yukarıdaki kod, aşağıdakine benzer şekilde derlenir:</span><span class="sxs-lookup"><span data-stu-id="19048-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="19048-140">Kod ilk kez çalıştırıldığında, `someFlag` `true` Oluşturucu şunları alır:</span><span class="sxs-lookup"><span data-stu-id="19048-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="19048-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="19048-141">Sequence</span></span> | <span data-ttu-id="19048-142">Tür</span><span class="sxs-lookup"><span data-stu-id="19048-142">Type</span></span>      | <span data-ttu-id="19048-143">Veriler</span><span class="sxs-lookup"><span data-stu-id="19048-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="19048-144">0</span><span class="sxs-lookup"><span data-stu-id="19048-144">0</span></span>        | <span data-ttu-id="19048-145">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="19048-145">Text node</span></span> | <span data-ttu-id="19048-146">Birinci</span><span class="sxs-lookup"><span data-stu-id="19048-146">First</span></span>  |
| <span data-ttu-id="19048-147">1</span><span class="sxs-lookup"><span data-stu-id="19048-147">1</span></span>        | <span data-ttu-id="19048-148">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="19048-148">Text node</span></span> | <span data-ttu-id="19048-149">Second</span><span class="sxs-lookup"><span data-stu-id="19048-149">Second</span></span> |

<span data-ttu-id="19048-150">Olduğunu düşünün `someFlag` `false` ve biçimlendirme yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="19048-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="19048-151">Bu kez, Oluşturucu şunları alır:</span><span class="sxs-lookup"><span data-stu-id="19048-151">This time, the builder receives:</span></span>

| <span data-ttu-id="19048-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="19048-152">Sequence</span></span> | <span data-ttu-id="19048-153">Tür</span><span class="sxs-lookup"><span data-stu-id="19048-153">Type</span></span>       | <span data-ttu-id="19048-154">Veriler</span><span class="sxs-lookup"><span data-stu-id="19048-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="19048-155">1</span><span class="sxs-lookup"><span data-stu-id="19048-155">1</span></span>        | <span data-ttu-id="19048-156">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="19048-156">Text node</span></span>  | <span data-ttu-id="19048-157">Second</span><span class="sxs-lookup"><span data-stu-id="19048-157">Second</span></span> |

<span data-ttu-id="19048-158">Çalışma zamanı bir fark gerçekleştirdiğinde, sıradaki öğenin `0` kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *düzenleme betiğini* oluşturur:</span><span class="sxs-lookup"><span data-stu-id="19048-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* :</span></span>

* <span data-ttu-id="19048-159">İlk metin düğümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="19048-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="19048-160">Program aracılığıyla sıra numaraları oluşturma sorunu</span><span class="sxs-lookup"><span data-stu-id="19048-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="19048-161">Aşağıdaki işleme Ağacı Oluşturucusu mantığını yazmanız yerine düşünün:</span><span class="sxs-lookup"><span data-stu-id="19048-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="19048-162">Şimdi ilk çıktı:</span><span class="sxs-lookup"><span data-stu-id="19048-162">Now, the first output is:</span></span>

| <span data-ttu-id="19048-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="19048-163">Sequence</span></span> | <span data-ttu-id="19048-164">Tür</span><span class="sxs-lookup"><span data-stu-id="19048-164">Type</span></span>      | <span data-ttu-id="19048-165">Veriler</span><span class="sxs-lookup"><span data-stu-id="19048-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="19048-166">0</span><span class="sxs-lookup"><span data-stu-id="19048-166">0</span></span>        | <span data-ttu-id="19048-167">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="19048-167">Text node</span></span> | <span data-ttu-id="19048-168">Birinci</span><span class="sxs-lookup"><span data-stu-id="19048-168">First</span></span>  |
| <span data-ttu-id="19048-169">1</span><span class="sxs-lookup"><span data-stu-id="19048-169">1</span></span>        | <span data-ttu-id="19048-170">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="19048-170">Text node</span></span> | <span data-ttu-id="19048-171">Second</span><span class="sxs-lookup"><span data-stu-id="19048-171">Second</span></span> |

<span data-ttu-id="19048-172">Bu sonuç önceki bir durum ile aynıdır, bu nedenle olumsuz bir sorun yoktur.</span><span class="sxs-lookup"><span data-stu-id="19048-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="19048-173">`someFlag``false`ikinci işleme ve çıktı:</span><span class="sxs-lookup"><span data-stu-id="19048-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="19048-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="19048-174">Sequence</span></span> | <span data-ttu-id="19048-175">Tür</span><span class="sxs-lookup"><span data-stu-id="19048-175">Type</span></span>      | <span data-ttu-id="19048-176">Veriler</span><span class="sxs-lookup"><span data-stu-id="19048-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="19048-177">0</span><span class="sxs-lookup"><span data-stu-id="19048-177">0</span></span>        | <span data-ttu-id="19048-178">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="19048-178">Text node</span></span> | <span data-ttu-id="19048-179">Second</span><span class="sxs-lookup"><span data-stu-id="19048-179">Second</span></span> |

<span data-ttu-id="19048-180">Bu kez, fark algoritması *iki* değişikliğin oluştuğunu görür ve algoritma aşağıdaki düzenleme betiğini üretir:</span><span class="sxs-lookup"><span data-stu-id="19048-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="19048-181">İlk metin düğümünün değerini olarak değiştirin `Second` .</span><span class="sxs-lookup"><span data-stu-id="19048-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="19048-182">İkinci metin düğümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="19048-182">Remove the second text node.</span></span>

<span data-ttu-id="19048-183">Sıra numaralarının oluşturulması, `if/else` dal ve döngülerin orijinal kodda bulunduğu yer hakkındaki tüm yararlı bilgileri kaybetti.</span><span class="sxs-lookup"><span data-stu-id="19048-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="19048-184">Bu, daha önce olduğu gibi bir fark **ile iki kez** sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="19048-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="19048-185">Bu, önemsiz bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="19048-185">This is a trivial example.</span></span> <span data-ttu-id="19048-186">Karmaşık ve derin iç içe yapıları ve özellikle döngülerle daha gerçekçi durumlarda performans maliyeti genellikle daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="19048-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="19048-187">Hangi döngü bloklarının veya dallarının eklendiğini veya kaldırıldığını hemen belirlemek yerine, fark algoritmasının işleme ağaçlarına göre belirlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="19048-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="19048-188">Bu genellikle, fark algoritması, eski ve yeni yapıların birbirleriyle nasıl ilişkilendirileceğiyle ilgili bilgi sahibi olduğu için daha uzun düzenleme betikleri oluşturma ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="19048-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="19048-189">Kılavuz ve ekibinizle</span><span class="sxs-lookup"><span data-stu-id="19048-189">Guidance and conclusions</span></span>

* <span data-ttu-id="19048-190">Sıra numaraları dinamik olarak oluşturulursa uygulama performansı de vardır.</span><span class="sxs-lookup"><span data-stu-id="19048-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="19048-191">Altyapı, derleme zamanında yakalanmadığı takdirde gerekli bilgiler bulunmadığından, çalışma zamanında kendi sıra numaralarını otomatik olarak oluşturamaz.</span><span class="sxs-lookup"><span data-stu-id="19048-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="19048-192">El ile uygulanan mantık uzun blokları yazmayın <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="19048-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="19048-193">Dosyaları tercih edin `.razor` ve derleyicinin sıra numaralarıyla uğraşmak için izin verin.</span><span class="sxs-lookup"><span data-stu-id="19048-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="19048-194">El ile <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> mantığın olmaması durumunda, uzun kod bloklarını çağrılarında kaydırılmış küçük parçalara ayırın <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> .</span><span class="sxs-lookup"><span data-stu-id="19048-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="19048-195">Her bölge kendi ayrı dizi numaralarına sahiptir, bu nedenle her bölge içinde sıfırdan (veya herhangi bir rastgele sayıdan) yeniden başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="19048-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="19048-196">Dizi numaraları sabit kodluysa, fark algoritması yalnızca değer değerinde sıra numaralarının artırılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="19048-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="19048-197">İlk değer ve boşluklar ilgisiz.</span><span class="sxs-lookup"><span data-stu-id="19048-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="19048-198">Tek bir seçenek, kod satırı numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp bir ya da yüzlerce (ya da tercih edilen aralığa) artırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19048-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="19048-199">:::no-loc(Blazor)::: sıra numaralarını kullanır, diğer ağaç dağıtma Kullanıcı arabirimi çerçeveleri bunları kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="19048-199">:::no-loc(Blazor)::: uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="19048-200">Dizi numaraları kullanıldığında, yayılma işlemi daha hızlı bir şekilde yapılır ve :::no-loc(Blazor)::: geliştiricilerin yazma dosyaları için otomatik olarak sıra numaralarıyla ilgilenen bir derleme adımının avantajına sahiptir `.razor` .</span><span class="sxs-lookup"><span data-stu-id="19048-200">Diffing is far faster when sequence numbers are used, and :::no-loc(Blazor)::: has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
