---
title: ASP.NET Core Blazor Gelişmiş senaryolar
author: guardrex
description: "' Deki gelişmiş senaryolar Blazor , El Ile RenderTreeBuilder mantığını bir uygulamaya nasıl dahil leyeceğinizi öğrenin."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 3345f545e230ada78e6c66fc9eb049060d5794d6
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851165"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="4a4d1-103">ASP.NET Core Blazor Gelişmiş senaryolar</span><span class="sxs-lookup"><span data-stu-id="4a4d1-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="4a4d1-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="4a4d1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a>Blazor<span data-ttu-id="4a4d1-105">Sunucu devre işleyicisi</span><span class="sxs-lookup"><span data-stu-id="4a4d1-105"> Server circuit handler</span></span>

Blazor<span data-ttu-id="4a4d1-106">Sunucu, kodun bir Kullanıcı devresi durumunda değişiklikler üzerinde kod çalıştırmaya izin veren bir *devhandler*tanımlamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-106"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="4a4d1-107">Devre işleyici, `CircuitHandler` uygulamanın hizmet kapsayıcısındaki sınıfından türeterek ve kayıt işleminden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="4a4d1-108">Bir devre işleyicinin aşağıdaki örneği açık SignalR bağlantıları izler:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="4a4d1-109">Devre işleyicileri DI kullanılarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="4a4d1-110">Kapsamlı örnekler, bir devrenin örneği başına oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="4a4d1-111">`TrackingCircuitHandler`Önceki örnekte kullanılarak, tüm devrelerin durumu izlenmelidir çünkü bir tek hizmet oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="4a4d1-112">Özel bir devre işleyicinin yöntemleri işlenmeyen bir özel durum oluşturuyorsam, özel durum sunucu devresi için önemli olur Blazor .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="4a4d1-113">Bir işleyicinin kodundaki veya yöntemleri çağrılan özel durumlara tolerans sağlamak için kodu bir veya daha fazla [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyiminde hata işleme ve günlüğe kaydetme ile sarın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="4a4d1-114">Bir kullanıcının bağlantısı kesilmediği ve Framework devre durumunu temizlemede bir devre dışı bırakıldığında, çerçeve devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="4a4d1-115">Kapsamı elden atılırken, uygulayan hiçbir devre kapsamlı dı hizmeti yok <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="4a4d1-116">Herhangi bir DI hizmeti, elden çıkarma sırasında işlenmeyen bir özel durum oluşturursa, çerçeve özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="4a4d1-117">El ile RenderTreeBuilder mantığı</span><span class="sxs-lookup"><span data-stu-id="4a4d1-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="4a4d1-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>bileşenleri ve öğeleri düzenlemek için yöntemler sağlar. bu sayede bileşenleri C# kodunda el ile oluşturma da dahil olmak üzere.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="4a4d1-119"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Bileşenlerini oluşturmak için kullanımı gelişmiş bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="4a4d1-120">Hatalı biçimlendirilmiş bir bileşen (örneğin, kapatılmamış bir biçimlendirme etiketi) tanımsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="4a4d1-121">Aşağıdaki `PetDetails` bileşeni, başka bir bileşende el ile yerleşik olarak kullanılabilecek şekilde göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="4a4d1-122">Aşağıdaki örnekte, `CreateComponent` yöntemindeki döngü üç `PetDetails` bileşen oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="4a4d1-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Bileşenleri (ve) oluşturmak için yöntemler çağrılırken `OpenComponent` `AddAttribute` , dizi numaraları kaynak kodu satır numaralarıdır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-123">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="4a4d1-124">BlazorFark algoritması, ayrı çağrı etkinleştirmeleri değil ayrı kod satırlarına karşılık gelen sıra numaralarına dayanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="4a4d1-125">Yöntemler içeren bir bileşen oluştururken <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , dizi numaralarına yönelik bağımsız değişkenleri kod olarak kodlayın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="4a4d1-126">**Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak kötü performansa neden olabilir.**</span><span class="sxs-lookup"><span data-stu-id="4a4d1-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="4a4d1-127">Daha fazla bilgi için bkz. [kod satırı numaralarıyla Ilgili sıra numaraları ve yürütme sırası çalışmıyor](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) bölümü.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="4a4d1-128">`BuiltContent`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="4a4d1-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="4a4d1-129">İçindeki türler, <xref:Microsoft.AspNetCore.Components.RenderTree> işleme işlemlerinin *sonuçlarının* işlenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="4a4d1-130">Bunlar, çerçeve uygulamasının iç ayrıntılardır Blazor .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="4a4d1-131">Bu türlerin *dengesizleşilmesi* ve gelecekteki sürümlerde değişikliğe tabi olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="4a4d1-132">Sıra numaraları, kod satırı numaralarıyla ilgilidir ve yürütme sırası değildir</span><span class="sxs-lookup"><span data-stu-id="4a4d1-132">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="4a4d1-133">bileşen dosyaları (*. Razor*) her zaman derlenir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-133"> component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="4a4d1-134">Derleme adımı, çalışma zamanında uygulama performansını geliştiren bilgileri eklemek için kullanılabilir olduğundan, kod yorumlanması üzerinde olası bir avantajdır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="4a4d1-135">Bu geliştirmelerin önemli bir örneği, *sıra numaraları*içerir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="4a4d1-136">Sıra numaraları, hangi çıkışların ayrı ve sıralı kod satırlarından geldiğini çalışma zamanına işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="4a4d1-137">Çalışma zamanı, doğrusal bir zamanda, genel ağaç farkı algoritması için genellikle mümkün olandan çok daha hızlı olan etkili ağaç SLA 'ları oluşturmak için bu bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="4a4d1-138">Aşağıdaki Razor bileşen (*. Razor*) dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="4a4d1-139">Yukarıdaki kod, aşağıdakine benzer şekilde derlenir:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="4a4d1-140">Kod ilk kez çalıştırıldığında, `someFlag` `true` Oluşturucu şunları alır:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="4a4d1-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="4a4d1-141">Sequence</span></span> | <span data-ttu-id="4a4d1-142">Tür</span><span class="sxs-lookup"><span data-stu-id="4a4d1-142">Type</span></span>      | <span data-ttu-id="4a4d1-143">Veriler</span><span class="sxs-lookup"><span data-stu-id="4a4d1-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="4a4d1-144">0</span><span class="sxs-lookup"><span data-stu-id="4a4d1-144">0</span></span>        | <span data-ttu-id="4a4d1-145">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="4a4d1-145">Text node</span></span> | <span data-ttu-id="4a4d1-146">İlk</span><span class="sxs-lookup"><span data-stu-id="4a4d1-146">First</span></span>  |
| <span data-ttu-id="4a4d1-147">1</span><span class="sxs-lookup"><span data-stu-id="4a4d1-147">1</span></span>        | <span data-ttu-id="4a4d1-148">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="4a4d1-148">Text node</span></span> | <span data-ttu-id="4a4d1-149">Saniye</span><span class="sxs-lookup"><span data-stu-id="4a4d1-149">Second</span></span> |

<span data-ttu-id="4a4d1-150">Olduğunu düşünün `someFlag` `false` ve biçimlendirme yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="4a4d1-151">Bu kez, Oluşturucu şunları alır:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-151">This time, the builder receives:</span></span>

| <span data-ttu-id="4a4d1-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="4a4d1-152">Sequence</span></span> | <span data-ttu-id="4a4d1-153">Tür</span><span class="sxs-lookup"><span data-stu-id="4a4d1-153">Type</span></span>       | <span data-ttu-id="4a4d1-154">Veriler</span><span class="sxs-lookup"><span data-stu-id="4a4d1-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="4a4d1-155">1</span><span class="sxs-lookup"><span data-stu-id="4a4d1-155">1</span></span>        | <span data-ttu-id="4a4d1-156">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="4a4d1-156">Text node</span></span>  | <span data-ttu-id="4a4d1-157">Saniye</span><span class="sxs-lookup"><span data-stu-id="4a4d1-157">Second</span></span> |

<span data-ttu-id="4a4d1-158">Çalışma zamanı bir fark gerçekleştirdiğinde, sıradaki öğenin `0` kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *düzenleme betiğini*oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="4a4d1-159">İlk metin düğümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="4a4d1-160">Program aracılığıyla sıra numaraları oluşturma sorunu</span><span class="sxs-lookup"><span data-stu-id="4a4d1-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="4a4d1-161">Aşağıdaki işleme Ağacı Oluşturucusu mantığını yazmanız yerine düşünün:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="4a4d1-162">Şimdi ilk çıktı:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-162">Now, the first output is:</span></span>

| <span data-ttu-id="4a4d1-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="4a4d1-163">Sequence</span></span> | <span data-ttu-id="4a4d1-164">Tür</span><span class="sxs-lookup"><span data-stu-id="4a4d1-164">Type</span></span>      | <span data-ttu-id="4a4d1-165">Veriler</span><span class="sxs-lookup"><span data-stu-id="4a4d1-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="4a4d1-166">0</span><span class="sxs-lookup"><span data-stu-id="4a4d1-166">0</span></span>        | <span data-ttu-id="4a4d1-167">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="4a4d1-167">Text node</span></span> | <span data-ttu-id="4a4d1-168">İlk</span><span class="sxs-lookup"><span data-stu-id="4a4d1-168">First</span></span>  |
| <span data-ttu-id="4a4d1-169">1</span><span class="sxs-lookup"><span data-stu-id="4a4d1-169">1</span></span>        | <span data-ttu-id="4a4d1-170">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="4a4d1-170">Text node</span></span> | <span data-ttu-id="4a4d1-171">Saniye</span><span class="sxs-lookup"><span data-stu-id="4a4d1-171">Second</span></span> |

<span data-ttu-id="4a4d1-172">Bu sonuç önceki bir durum ile aynıdır, bu nedenle olumsuz bir sorun yoktur.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="4a4d1-173">`someFlag``false`ikinci işleme ve çıktı:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="4a4d1-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="4a4d1-174">Sequence</span></span> | <span data-ttu-id="4a4d1-175">Tür</span><span class="sxs-lookup"><span data-stu-id="4a4d1-175">Type</span></span>      | <span data-ttu-id="4a4d1-176">Veriler</span><span class="sxs-lookup"><span data-stu-id="4a4d1-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="4a4d1-177">0</span><span class="sxs-lookup"><span data-stu-id="4a4d1-177">0</span></span>        | <span data-ttu-id="4a4d1-178">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="4a4d1-178">Text node</span></span> | <span data-ttu-id="4a4d1-179">Saniye</span><span class="sxs-lookup"><span data-stu-id="4a4d1-179">Second</span></span> |

<span data-ttu-id="4a4d1-180">Bu kez, fark algoritması *iki* değişikliğin oluştuğunu görür ve algoritma aşağıdaki düzenleme betiğini üretir:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="4a4d1-181">İlk metin düğümünün değerini olarak değiştirin `Second` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="4a4d1-182">İkinci metin düğümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-182">Remove the second text node.</span></span>

<span data-ttu-id="4a4d1-183">Sıra numaralarının oluşturulması, `if/else` dal ve döngülerin orijinal kodda bulunduğu yer hakkındaki tüm yararlı bilgileri kaybetti.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="4a4d1-184">Bu, daha önce olduğu gibi bir fark **ile iki kez** sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="4a4d1-185">Bu, önemsiz bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-185">This is a trivial example.</span></span> <span data-ttu-id="4a4d1-186">Karmaşık ve derin iç içe yapıları ve özellikle döngülerle daha gerçekçi durumlarda performans maliyeti genellikle daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="4a4d1-187">Hangi döngü bloklarının veya dallarının eklendiğini veya kaldırıldığını hemen belirlemek yerine, fark algoritmasının işleme ağaçlarına göre belirlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="4a4d1-188">Bu genellikle, fark algoritması, eski ve yeni yapıların birbirleriyle nasıl ilişkilendirileceğiyle ilgili bilgi sahibi olduğu için daha uzun düzenleme betikleri oluşturma ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="4a4d1-189">Kılavuz ve ekibinizle</span><span class="sxs-lookup"><span data-stu-id="4a4d1-189">Guidance and conclusions</span></span>

* <span data-ttu-id="4a4d1-190">Sıra numaraları dinamik olarak oluşturulursa uygulama performansı de vardır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="4a4d1-191">Altyapı, derleme zamanında yakalanmadığı takdirde gerekli bilgiler bulunmadığından, çalışma zamanında kendi sıra numaralarını otomatik olarak oluşturamaz.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="4a4d1-192">El ile uygulanan mantık uzun blokları yazmayın <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="4a4d1-193">*. Razor* dosyalarını tercih edin ve derleyicinin sıra numaralarıyla uğraşmak için izin verin.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="4a4d1-194">El ile <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> mantığın olmaması durumunda, uzun kod bloklarını çağrılarında kaydırılmış küçük parçalara ayırın <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="4a4d1-195">Her bölge kendi ayrı dizi numaralarına sahiptir, bu nedenle her bölge içinde sıfırdan (veya herhangi bir rastgele sayıdan) yeniden başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="4a4d1-196">Dizi numaraları sabit kodluysa, fark algoritması yalnızca değer değerinde sıra numaralarının artırılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="4a4d1-197">İlk değer ve boşluklar ilgisiz.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="4a4d1-198">Tek bir seçenek, kod satırı numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp bir ya da yüzlerce (ya da tercih edilen aralığa) artırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="4a4d1-199">sıra numaralarını kullanır, diğer ağaç dağıtma Kullanıcı arabirimi çerçeveleri bunları kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="4a4d1-200">Dizi numaraları kullanıldığında, yayılma çok daha hızlıdır ve Blazor bir derleme adımından yararlanarak *. Razor* dosyaları yazan geliştiriciler için otomatik olarak sıra numaralarıyla ilgilenen bir derleme adımının avantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="4a4d1-201">Sunucu uygulamalarında büyük veri aktarımları gerçekleştirin Blazor</span><span class="sxs-lookup"><span data-stu-id="4a4d1-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="4a4d1-202">Bazı senaryolarda, JavaScript ve arasında büyük miktarlarda veri aktarılmalıdır Blazor .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="4a4d1-203">Genellikle, büyük veri aktarımları şu durumlarda oluşur:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="4a4d1-204">Tarayıcı dosya sistemi API 'Leri bir dosyayı karşıya yüklemek veya indirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="4a4d1-205">Üçüncü taraf kitaplığı ile birlikte çalışma gerekir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="4a4d1-206">BlazorSunucuda, performans sorunlarına neden olabilecek tek büyük mesajların geçirilmesini engellemek için bir sınırlama vardır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="4a4d1-207">JavaScript arasında veri aktaran kodu geliştirirken aşağıdaki kılavuzu göz önünde bulundurun Blazor :</span><span class="sxs-lookup"><span data-stu-id="4a4d1-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="4a4d1-208">Verileri daha küçük parçalara dilimleyin ve tüm veriler sunucu tarafından alınana kadar veri segmentlerini sırayla gönderin.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="4a4d1-209">JavaScript ve C# kodunda büyük nesneler ayırmayın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="4a4d1-210">Veri gönderirken veya alırken uzun süreler için ana UI iş parçacığını engellemez.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="4a4d1-211">İşlem tamamlandığında veya iptal edildiğinde tüketilen tüm belleği boşaltın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="4a4d1-212">Güvenlik amaçları için aşağıdaki ek gereksinimleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="4a4d1-213">Geçirilebilen en büyük dosya veya veri boyutunu bildirin.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="4a4d1-214">İstemciden sunucuya en düşük karşıya yükleme hızını bildirin.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="4a4d1-215">Veriler, sunucu tarafından alındıktan sonra şu olabilir:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="4a4d1-216">Tüm segmentler toplanana kadar bir bellek arabelleğinde geçici olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="4a4d1-217">Hemen tüketildi.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-217">Consumed immediately.</span></span> <span data-ttu-id="4a4d1-218">Örneğin, veriler bir veritabanında hemen depolanabilir veya her bir segment alındığında diske yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="4a4d1-219">Aşağıdaki dosya Yükleyici sınıfı istemcisiyle JS birlikte çalışabilirliği yönetir.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="4a4d1-220">Uploader sınıfı, JS birlikte çalışması için şunu kullanır:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="4a4d1-221">Veri segmenti göndermek için istemciyi yoklayın.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="4a4d1-222">Yoklama zaman aşımına uğrarsa işlemi iptal edin.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="4a4d1-223">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-223">In the preceding example:</span></span>

* <span data-ttu-id="4a4d1-224">, `maxBase64SegmentSize` ' A ayarlanır `8192` , ' den hesaplanır `maxBase64SegmentSize = segmentSize * 4 / 3` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="4a4d1-225">Düşük düzey .NET Core bellek yönetimi API 'Leri, bellek kesimlerini içindeki sunucuda depolamak için kullanılır `uploadedSegments` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="4a4d1-226">Bir `ReceiveFile` Yöntem, JS birlikte çalışması aracılığıyla karşıya yüklemeyi işlemek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="4a4d1-227">Dosya boyutu, ile JS birlikte çalışma üzerinden bayt olarak belirlenir `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="4a4d1-228">Alacak segmentlerin sayısı ' de hesaplanıp depolanır `numberOfSegments` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="4a4d1-229">Kesimleri, `for` Ile js birlikte çalışma aracılığıyla bir döngüde istenir `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="4a4d1-230">Tüm kesimler ancak son, kod çözmede önce 8.192 bayt olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="4a4d1-231">İstemci verileri verimli bir şekilde gönderilmeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="4a4d1-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="4a4d1-232">Alınan her segment için, ile kod çözme işleminden önce denetimler gerçekleştirilir <xref:System.Convert.TryFromBase64String%2A> .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="4a4d1-233">Karşıya yükleme tamamlandıktan sonra verileri içeren bir akış New <xref:System.IO.Stream> () olarak döndürülür `SegmentedStream` .</span><span class="sxs-lookup"><span data-stu-id="4a4d1-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="4a4d1-234">Kesimli akış sınıfı, kesim listesini ReadOnly olmayan salt okunur olarak kullanıma sunar <xref:System.IO.Stream> :</span><span class="sxs-lookup"><span data-stu-id="4a4d1-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="4a4d1-235">Aşağıdaki kod, verileri almak için JavaScript işlevlerini uygular:</span><span class="sxs-lookup"><span data-stu-id="4a4d1-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
