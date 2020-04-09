---
title: ASP.NET Blazor Core gelişmiş senaryoları
author: guardrex
description: Manuel RenderTreeBuilder Blazormantığını bir uygulamaya nasıl dahil edebilirsiniz dahil olmak üzere gelişmiş senaryolar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659455"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="d570e-103">core Blazor gelişmiş senaryoları ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d570e-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="d570e-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d570e-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="d570e-105">Blazor Server devre işleyicisi</span><span class="sxs-lookup"><span data-stu-id="d570e-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="d570e-106">Blazor Server, kodun bir *devre işleyicisini*tanımlamasına izin verir, bu da kullanıcının devresinin durumuna yapılan değişikliklerde kod çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d570e-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="d570e-107">Bir devre işleyicisi, sınıfın `CircuitHandler` uygulamanın hizmet konteynerine kaydedilmesi yle uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="d570e-108">Bir devre işleyicisi aşağıdaki örnek açık SignalR bağlantıları izler:</span><span class="sxs-lookup"><span data-stu-id="d570e-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="d570e-109">Devre işleyicileri DI kullanılarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d570e-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="d570e-110">Kapsamlı örnekler, bir devrenin örneğine göre oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d570e-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="d570e-111">Önceki `TrackingCircuitHandler` örnekte kullanarak, tüm devrelerin durumu izlenmesi gerektiğinden, singleton hizmeti oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d570e-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="d570e-112">Özel bir devre işleyicisinin yöntemleri işlenmemiş bir özel durum atarsa, özel durum Blazor Server devresi için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="d570e-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="d570e-113">İşleyicinin kodundaki veya çağrılan yöntemlerdeki özel durumları tolere etmek için, kodu hata işleme ve günlüğe kaydetme içeren bir veya daha fazla [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimine sarın.</span><span class="sxs-lookup"><span data-stu-id="d570e-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="d570e-114">Bir kullanıcı bağlantısı kesildiği nden ve çerçeve devre durumunu temizlediği için bir devre sona erdiğinde, çerçeve devrenin DI kapsamını ortadan kaldırmıştır.</span><span class="sxs-lookup"><span data-stu-id="d570e-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="d570e-115">Kapsamı atmak, devreye sahip tüm devre kapsamlı <xref:System.IDisposable?displayProperty=fullName>DI hizmetlerini ortadan çıkarır.</span><span class="sxs-lookup"><span data-stu-id="d570e-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="d570e-116">Herhangi bir DI hizmeti imha sırasında işlenmemiş bir özel durum atarsa, çerçeve özel durumu kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d570e-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="d570e-117">Manuel RenderTreeBuilder mantığı</span><span class="sxs-lookup"><span data-stu-id="d570e-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="d570e-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`c# kodunda bileşenleri el ile oluşturmak da dahil olmak üzere bileşenleri ve öğeleri işlemek için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="d570e-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="d570e-119">Bileşenleri `RenderTreeBuilder` oluşturmak için kullanımı gelişmiş bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="d570e-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="d570e-120">Hatalı biçimlendirilmiş bir bileşen (örneğin, kapalı biçimlendirme etiketi) tanımlanmamış davranışlara neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="d570e-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="d570e-121">Başka bir `PetDetails` bileşende el ile oluşturulabilen aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d570e-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="d570e-122">Aşağıdaki örnekte, yöntemdeki `CreateComponent` döngü üç `PetDetails` bileşen oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d570e-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="d570e-123">Bileşenleri `RenderTreeBuilder` oluşturmak için yöntemleri`OpenComponent` ararken ( ve `AddAttribute`), sıra numaraları kaynak kodu satır numaralarıdır.</span><span class="sxs-lookup"><span data-stu-id="d570e-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="d570e-124">Blazor fark algoritması, farklı çağrı çağrılarına değil, farklı kod satırlarına karşılık gelen sıra numaralarına dayanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="d570e-125">Yöntemlerle `RenderTreeBuilder` bir bileşen oluştururken, sıra numaraları için bağımsız değişkenleri kodlayın.</span><span class="sxs-lookup"><span data-stu-id="d570e-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="d570e-126">**Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak düşük performansa yol açabilir.**</span><span class="sxs-lookup"><span data-stu-id="d570e-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="d570e-127">Daha fazla bilgi için, [Sıra numaralarının yürütme sırası bölümüyle değil, kod satır numaralarıyla ilgili](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="d570e-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="d570e-128">`BuiltContent`Bileşen:</span><span class="sxs-lookup"><span data-stu-id="d570e-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="d570e-129">İşleme `Microsoft.AspNetCore.Components.RenderTree` işlemlerinin *sonuçlarının* işlenmesine izin veren türler.</span><span class="sxs-lookup"><span data-stu-id="d570e-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="d570e-130">Bunlar Blazor çerçeve uygulamasının iç detaylarıdır.</span><span class="sxs-lookup"><span data-stu-id="d570e-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="d570e-131">Bu tür *kararsız* ve gelecekteki sürümlerde değişebilir olarak kabul edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d570e-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="d570e-132">Sıra numaraları, yürütme sırası ile değil, kod satır numaralarıyla ilgilidir</span><span class="sxs-lookup"><span data-stu-id="d570e-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="d570e-133">Jilet bileşen ilerleç dosyaları (*.razor*) her zaman derlenir.</span><span class="sxs-lookup"><span data-stu-id="d570e-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="d570e-134">Derleme, çalışma zamanında uygulama performansını artıran bilgileri enjekte etmek için kullanılabildiği için kod yorumlamaya göre potansiyel bir avantajdır.</span><span class="sxs-lookup"><span data-stu-id="d570e-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="d570e-135">Bu geliştirmelerin önemli bir örneği *sıra numaralarını*içerir.</span><span class="sxs-lookup"><span data-stu-id="d570e-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="d570e-136">Sıra numaraları, hangi çıktıların farklı ve sıralanmış kod satırlarından geldiğini çalışma zamanı olarak gösterir.</span><span class="sxs-lookup"><span data-stu-id="d570e-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="d570e-137">Çalışma zamanı, genel bir ağaç diff algoritması için normalde mümkün olandan çok daha hızlı olan doğrusal zamanda verimli ağaç farkları oluşturmak için bu bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="d570e-138">Aşağıdaki Razor bileşenini (*.razor*) dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d570e-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="d570e-139">Önceki kod aşağıdaki gibi bir şey için derler:</span><span class="sxs-lookup"><span data-stu-id="d570e-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="d570e-140">Kod ilk kez yürütüldüğünde, `someFlag` oluşturucu `true`alır:</span><span class="sxs-lookup"><span data-stu-id="d570e-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="d570e-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="d570e-141">Sequence</span></span> | <span data-ttu-id="d570e-142">Tür</span><span class="sxs-lookup"><span data-stu-id="d570e-142">Type</span></span>      | <span data-ttu-id="d570e-143">Veriler</span><span class="sxs-lookup"><span data-stu-id="d570e-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="d570e-144">0</span><span class="sxs-lookup"><span data-stu-id="d570e-144">0</span></span>        | <span data-ttu-id="d570e-145">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="d570e-145">Text node</span></span> | <span data-ttu-id="d570e-146">İlk</span><span class="sxs-lookup"><span data-stu-id="d570e-146">First</span></span>  |
| <span data-ttu-id="d570e-147">1</span><span class="sxs-lookup"><span data-stu-id="d570e-147">1</span></span>        | <span data-ttu-id="d570e-148">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="d570e-148">Text node</span></span> | <span data-ttu-id="d570e-149">Saniye</span><span class="sxs-lookup"><span data-stu-id="d570e-149">Second</span></span> |

<span data-ttu-id="d570e-150">Bunun `someFlag` olduğunu `false`ve biçimlendirmenin yeniden işlenir olduğunu düşünün.</span><span class="sxs-lookup"><span data-stu-id="d570e-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="d570e-151">Bu kez, oluşturucu alır:</span><span class="sxs-lookup"><span data-stu-id="d570e-151">This time, the builder receives:</span></span>

| <span data-ttu-id="d570e-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="d570e-152">Sequence</span></span> | <span data-ttu-id="d570e-153">Tür</span><span class="sxs-lookup"><span data-stu-id="d570e-153">Type</span></span>       | <span data-ttu-id="d570e-154">Veriler</span><span class="sxs-lookup"><span data-stu-id="d570e-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="d570e-155">1</span><span class="sxs-lookup"><span data-stu-id="d570e-155">1</span></span>        | <span data-ttu-id="d570e-156">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="d570e-156">Text node</span></span>  | <span data-ttu-id="d570e-157">Saniye</span><span class="sxs-lookup"><span data-stu-id="d570e-157">Second</span></span> |

<span data-ttu-id="d570e-158">Çalışma zamanı bir diff gerçekleştirdiğinde, sıralı `0` öğenin kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *edit komut dosyası*oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d570e-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="d570e-159">İlk metin düğümlerini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="d570e-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="d570e-160">Programlı sıra numaraları oluşturma sorunu</span><span class="sxs-lookup"><span data-stu-id="d570e-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="d570e-161">Bunun yerine aşağıdaki render ağacı oluşturucu mantığı yazdı düşünün:</span><span class="sxs-lookup"><span data-stu-id="d570e-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="d570e-162">Şimdi, ilk çıkış:</span><span class="sxs-lookup"><span data-stu-id="d570e-162">Now, the first output is:</span></span>

| <span data-ttu-id="d570e-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="d570e-163">Sequence</span></span> | <span data-ttu-id="d570e-164">Tür</span><span class="sxs-lookup"><span data-stu-id="d570e-164">Type</span></span>      | <span data-ttu-id="d570e-165">Veriler</span><span class="sxs-lookup"><span data-stu-id="d570e-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="d570e-166">0</span><span class="sxs-lookup"><span data-stu-id="d570e-166">0</span></span>        | <span data-ttu-id="d570e-167">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="d570e-167">Text node</span></span> | <span data-ttu-id="d570e-168">İlk</span><span class="sxs-lookup"><span data-stu-id="d570e-168">First</span></span>  |
| <span data-ttu-id="d570e-169">1</span><span class="sxs-lookup"><span data-stu-id="d570e-169">1</span></span>        | <span data-ttu-id="d570e-170">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="d570e-170">Text node</span></span> | <span data-ttu-id="d570e-171">Saniye</span><span class="sxs-lookup"><span data-stu-id="d570e-171">Second</span></span> |

<span data-ttu-id="d570e-172">Bu sonuç önceki durumla aynıdır, bu nedenle olumsuz bir sorun yoktur.</span><span class="sxs-lookup"><span data-stu-id="d570e-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="d570e-173">`someFlag`ikinci `false` işleme ve çıktı:</span><span class="sxs-lookup"><span data-stu-id="d570e-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="d570e-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="d570e-174">Sequence</span></span> | <span data-ttu-id="d570e-175">Tür</span><span class="sxs-lookup"><span data-stu-id="d570e-175">Type</span></span>      | <span data-ttu-id="d570e-176">Veriler</span><span class="sxs-lookup"><span data-stu-id="d570e-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="d570e-177">0</span><span class="sxs-lookup"><span data-stu-id="d570e-177">0</span></span>        | <span data-ttu-id="d570e-178">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="d570e-178">Text node</span></span> | <span data-ttu-id="d570e-179">Saniye</span><span class="sxs-lookup"><span data-stu-id="d570e-179">Second</span></span> |

<span data-ttu-id="d570e-180">Bu kez, diff algoritması *iki* değişiklik oluştuğunu görür ve algoritma aşağıdaki düzenle komut dosyası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d570e-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="d570e-181">İlk metin düğümünün değerini ' `Second`le değiştirin</span><span class="sxs-lookup"><span data-stu-id="d570e-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="d570e-182">İkinci metin düğümlerini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="d570e-182">Remove the second text node.</span></span>

<span data-ttu-id="d570e-183">Sıra numaralarının üretilmesi, `if/else` dalların ve döngülerin özgün kodda nerede bulunduğuna ilişkin tüm yararlı bilgileri kaybetmiştir.</span><span class="sxs-lookup"><span data-stu-id="d570e-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="d570e-184">Bu, öncekinin **iki katı kadar bir** diff ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="d570e-185">Bu önemsiz bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="d570e-185">This is a trivial example.</span></span> <span data-ttu-id="d570e-186">Karmaşık ve derin iç içe yapılarile daha gerçekçi durumlarda ve özellikle döngüler ile, performans maliyeti genellikle daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="d570e-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="d570e-187">Hangi döngü bloklarının veya dalların eklendiğini veya kaldırıldığını hemen belirlemek yerine, diff algoritmasının render ağaçlarına derinden yeniden lanetlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d570e-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="d570e-188">Diff algoritması eski ve yeni yapıların birbiriyle nasıl ilişkili olduğu hakkında yanlış bilgilendirildiği için, bu genellikle daha uzun düzenkomut dosyaları oluşturmak zorunda sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="d570e-189">Rehberlik ve sonuçlar</span><span class="sxs-lookup"><span data-stu-id="d570e-189">Guidance and conclusions</span></span>

* <span data-ttu-id="d570e-190">Sıra numaraları dinamik olarak oluşturulursa uygulama performansı zarar görür.</span><span class="sxs-lookup"><span data-stu-id="d570e-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="d570e-191">Derleme zamanında yakalanmadığı sürece gerekli bilgiler bulunmadığından, çerçeve çalışma zamanında otomatik olarak kendi sıra numaralarını oluşturamaz.</span><span class="sxs-lookup"><span data-stu-id="d570e-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="d570e-192">El ile uygulanan `RenderTreeBuilder` mantığın uzun bloklarını yazmayın.</span><span class="sxs-lookup"><span data-stu-id="d570e-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="d570e-193">.razor dosyalarını tercih *edin* ve derleyicinin sıra numaralarıyla ilgilenmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="d570e-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="d570e-194">El ile `RenderTreeBuilder` mantıktan kaçınamıyorsanız, uzun kod bloklarını `OpenRegion` / `CloseRegion` çağrılara sarılmış daha küçük parçalara bölün.</span><span class="sxs-lookup"><span data-stu-id="d570e-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="d570e-195">Her bölgenin kendi sıra numaraları alanı vardır, böylece her bölge içindeki sıfırdan (veya başka bir rasgele sayıdan) yeniden başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d570e-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="d570e-196">Sıra numaraları kodlanmışsa, diff algoritması yalnızca sıra numaralarının değer artışını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d570e-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="d570e-197">İlk değer ve boşluklar alakasızdır.</span><span class="sxs-lookup"><span data-stu-id="d570e-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="d570e-198">Yasal seçeneklerden biri, kod satır numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp birler veya yüzlerce (veya tercih edilen herhangi bir aralık) ile artırmaktır.</span><span class="sxs-lookup"><span data-stu-id="d570e-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="d570e-199">sıra numaralarını kullanırken, diğer ağaç dağılayan UI çerçeveleri bunları kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="d570e-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="d570e-200">Dizi numaraları kullanıldığında dağıtma çok daha Blazor hızlıdır ve *.razor* dosyaları yazan geliştiriciler için sıra numaralarıyla otomatik olarak ilgilenen bir derleme adımının avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d570e-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="d570e-201">Sunucu uygulamalarında Blazor büyük veri aktarımları gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="d570e-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="d570e-202">Bazı senaryolarda, büyük miktarda veri JavaScript ve Blazor.</span><span class="sxs-lookup"><span data-stu-id="d570e-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="d570e-203">Genellikle, büyük veri aktarımları oluşur:</span><span class="sxs-lookup"><span data-stu-id="d570e-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="d570e-204">Tarayıcı dosya sistemi API'leri bir dosyayı yüklemek veya indirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d570e-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="d570e-205">Üçüncü taraf kitaplığı ile interop gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d570e-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="d570e-206">Sunucu'da, Blazor performans sorunlarına neden olabilecek tek büyük iletilerin geçmesini önlemek için bir sınırlama vardır.</span><span class="sxs-lookup"><span data-stu-id="d570e-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="d570e-207">JavaScript ile Blazoraşağıdaki arasında veri aktaran kod geliştirirken aşağıdaki kılavuzu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d570e-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="d570e-208">Verileri daha küçük parçalara dilimleyin ve tüm veriler sunucu tarafından alınana kadar veri bölümlerini sırayla gönderin.</span><span class="sxs-lookup"><span data-stu-id="d570e-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="d570e-209">JavaScript ve C# kodunda büyük nesneleri ayırmayın.</span><span class="sxs-lookup"><span data-stu-id="d570e-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="d570e-210">Veri gönderirken veya alırken ana Web Bilgisi iş parçacığının uzun süreler için engellenmesini.</span><span class="sxs-lookup"><span data-stu-id="d570e-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="d570e-211">İşlem tamamlandığında veya iptal edildiğinde tüketilen tüm bellekleri boşaltın.</span><span class="sxs-lookup"><span data-stu-id="d570e-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="d570e-212">Güvenlik amaçları için aşağıdaki ek gereksinimleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="d570e-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="d570e-213">Geçirilebilen en büyük dosyayı veya veri boyutunu bildirin.</span><span class="sxs-lookup"><span data-stu-id="d570e-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="d570e-214">İstemciden sunucuya minimum yükleme oranını bildirin.</span><span class="sxs-lookup"><span data-stu-id="d570e-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="d570e-215">Veriler sunucu tarafından alındıktan sonra, veriler aşağıdaki ler olabilir:</span><span class="sxs-lookup"><span data-stu-id="d570e-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="d570e-216">Tüm kesimler toplanana kadar geçici olarak bir bellek arabelleğinde saklanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="d570e-217">Hemen tüketilir.</span><span class="sxs-lookup"><span data-stu-id="d570e-217">Consumed immediately.</span></span> <span data-ttu-id="d570e-218">Örneğin, veriler hemen bir veritabanında depolanabilir veya her kesim alınırken diske yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="d570e-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="d570e-219">Aşağıdaki dosya yükleyici sınıfı istemci ile JS interop işler.</span><span class="sxs-lookup"><span data-stu-id="d570e-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="d570e-220">Yükleyici sınıfı JS interop kullanır:</span><span class="sxs-lookup"><span data-stu-id="d570e-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="d570e-221">Bir veri kesimi göndermek için istemciyi yokla.</span><span class="sxs-lookup"><span data-stu-id="d570e-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="d570e-222">Yoklama süreleri dolursa işlemi iptal edin.</span><span class="sxs-lookup"><span data-stu-id="d570e-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="d570e-223">Önceki örnekte:</span><span class="sxs-lookup"><span data-stu-id="d570e-223">In the preceding example:</span></span>

* <span data-ttu-id="d570e-224">'den `_maxBase64SegmentSize` hesaplanan `8192` `_maxBase64SegmentSize = _segmentSize * 4 / 3`' olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="d570e-225">Düşük düzeyli .NET Core bellek yönetimi API'leri sunucudaki `_uploadedSegments`bellek bölümlerini '' de depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d570e-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="d570e-226">JS interop üzerinden yükleme işlemek için bir `ReceiveFile` yöntem kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d570e-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="d570e-227">Dosya boyutu JS interop üzerinden `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`bayt olarak belirlenir.</span><span class="sxs-lookup"><span data-stu-id="d570e-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="d570e-228">Alınacak segment sayısı hesaplanır ve 'de `numberOfSegments`saklanır.</span><span class="sxs-lookup"><span data-stu-id="d570e-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="d570e-229">Segmentler JS interop `for` ile bir döngü `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`içinde istenir.</span><span class="sxs-lookup"><span data-stu-id="d570e-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="d570e-230">Tüm segmentler ama son çözme önce 8.192 bayt olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d570e-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="d570e-231">İstemci verileri verimli bir şekilde göndermek zorunda kalır.</span><span class="sxs-lookup"><span data-stu-id="d570e-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="d570e-232">Alınan her kesim için denetimler' <xref:System.Convert.TryFromBase64String*>in '' ile çözülmesiönce gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d570e-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="d570e-233">Yükleme tamamlandıktan sonra verilerle <xref:System.IO.Stream> birlikte`SegmentedStream`bir akış yeni ( ) olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d570e-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="d570e-234">Bölümlenmiş akış sınıfı, segmentlerin listesini yalnızca aranamayan <xref:System.IO.Stream>bir bölüm olarak ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="d570e-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
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

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

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

<span data-ttu-id="d570e-235">Aşağıdaki kod verileri almak için JavaScript işlevlerini uygular:</span><span class="sxs-lookup"><span data-stu-id="d570e-235">The following code implements JavaScript functions to receive the data:</span></span>

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
