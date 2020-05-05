---
title: ASP.NET Core bellek yönetimi ve desenleri
author: rick-anderson
description: ASP.NET Core ' de belleğin nasıl yönetildiğini ve çöp toplayıcı 'nın (GC) nasıl çalıştığını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/memory
ms.openlocfilehash: db6f8e867fc83a211170aa59f5bad604d9c2730d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776122"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="a9829-103">ASP.NET Core 'de bellek yönetimi ve çöp toplama (GC)</span><span class="sxs-lookup"><span data-stu-id="a9829-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="a9829-104">[Sébastien Ros](https://github.com/sebastienros) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="a9829-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9829-105">Bellek yönetimi, .NET gibi yönetilen bir çerçevede bile karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="a9829-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="a9829-106">Bellek sorunlarını analiz etmek ve anlamak zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="a9829-107">Bu makalede:</span><span class="sxs-lookup"><span data-stu-id="a9829-107">This article:</span></span>

* <span data-ttu-id="a9829-108">, Çok fazla *bellek sızıntısı* ve *GC çalışmasız* sorunlar tarafından tetiklendi.</span><span class="sxs-lookup"><span data-stu-id="a9829-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="a9829-109">Bu sorunların çoğu, bellek tüketiminin .NET Core 'da nasıl çalıştığını Anlamamasından veya nasıl ölçüleceğini anlayamadığında oluşur.</span><span class="sxs-lookup"><span data-stu-id="a9829-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="a9829-110">Sorunlu bellek kullanımını gösterir ve alternatif yaklaşımlar önerir.</span><span class="sxs-lookup"><span data-stu-id="a9829-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="a9829-111">Çöp toplama (GC) .NET Core 'da nasıl kullanılır</span><span class="sxs-lookup"><span data-stu-id="a9829-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="a9829-112">GC, her segmentin bitişik bellek aralığı olduğu yığın kesimlerini ayırır.</span><span class="sxs-lookup"><span data-stu-id="a9829-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="a9829-113">Yığına yerleştirilmiş nesneler 3 nesilden birine kategorize edilir: 0, 1 veya 2.</span><span class="sxs-lookup"><span data-stu-id="a9829-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="a9829-114">Oluşturma, GC 'nin, uygulama tarafından artık başvurulmayan yönetilen nesneler üzerinde bellek serbest bırakmaya yönelik sıklığı belirler.</span><span class="sxs-lookup"><span data-stu-id="a9829-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="a9829-115">Daha düşük numaralandırılmış nesiller GC daha sık kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="a9829-116">Nesneler, yaşam sürelerinin ömrü temelinde bir neslin diğerine taşınır.</span><span class="sxs-lookup"><span data-stu-id="a9829-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="a9829-117">Nesneler daha uzun bir süre içinde yaşlılarsa daha yüksek bir oluşturmaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="a9829-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="a9829-118">Daha önce belirtildiği gibi, daha yüksek neslikler GC 'yi daha düşüktür.</span><span class="sxs-lookup"><span data-stu-id="a9829-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="a9829-119">Kısa vadeli süreli nesneler her zaman nesil 0 ' da kalır.</span><span class="sxs-lookup"><span data-stu-id="a9829-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="a9829-120">Örneğin, bir Web isteğinin ömrü boyunca başvurulan nesneler kısa ömürlü değildir.</span><span class="sxs-lookup"><span data-stu-id="a9829-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="a9829-121">Uygulama düzeyi [tekton](xref:fundamentals/dependency-injection#service-lifetimes) genellikle 2. nesil 'e geçirilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="a9829-122">ASP.NET Core bir uygulama başlatıldığında GC:</span><span class="sxs-lookup"><span data-stu-id="a9829-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="a9829-123">İlk yığın kesimleri için bazı belleği ayırır.</span><span class="sxs-lookup"><span data-stu-id="a9829-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="a9829-124">Çalışma zamanı yüklenirken belleğin küçük bir bölümünü kaydeder.</span><span class="sxs-lookup"><span data-stu-id="a9829-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="a9829-125">Önceki bellek ayırmaları performans nedenleriyle yapılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="a9829-126">Performans avantajı, ardışık bellekteki yığın kesimlerinden gelir.</span><span class="sxs-lookup"><span data-stu-id="a9829-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="a9829-127">GC 'yi çağırın. Topladıktan</span><span class="sxs-lookup"><span data-stu-id="a9829-127">Call GC.Collect</span></span>

<span data-ttu-id="a9829-128">[GC çağrılıyor. Açıkça topla](xref:System.GC.Collect*) :</span><span class="sxs-lookup"><span data-stu-id="a9829-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="a9829-129">, Üretim ASP.NET Core uygulamaları tarafından **yapılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="a9829-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="a9829-130">Bellek sızıntılarını araştırırken faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="a9829-131">İnceleme yaparken, GC 'nin tüm sallaştırılmış nesneleri bellekten kaldırdığını doğrular, böylece bellek ölçülenebilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="a9829-132">Uygulamanın bellek kullanımını analiz etme</span><span class="sxs-lookup"><span data-stu-id="a9829-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="a9829-133">Adanmış araçlar bellek kullanımının analiz edilmesine yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="a9829-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="a9829-134">Nesne başvurularını sayma</span><span class="sxs-lookup"><span data-stu-id="a9829-134">Counting object references</span></span>
- <span data-ttu-id="a9829-135">GC 'nin CPU kullanımında ne kadar etkili olduğunu ölçme</span><span class="sxs-lookup"><span data-stu-id="a9829-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="a9829-136">Her nesil için kullanılan bellek alanını ölçme</span><span class="sxs-lookup"><span data-stu-id="a9829-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="a9829-137">Bellek kullanımını çözümlemek için aşağıdaki araçları kullanın:</span><span class="sxs-lookup"><span data-stu-id="a9829-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="a9829-138">[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace): üretim makinelerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="a9829-139">Visual Studio hata ayıklayıcısı olmadan bellek kullanımını analiz etme</span><span class="sxs-lookup"><span data-stu-id="a9829-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="a9829-140">Visual Studio’da bellek kullanımının profilini oluşturma</span><span class="sxs-lookup"><span data-stu-id="a9829-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="a9829-141">Bellek sorunlarını algılama</span><span class="sxs-lookup"><span data-stu-id="a9829-141">Detecting memory issues</span></span>

<span data-ttu-id="a9829-142">Görev Yöneticisi, bir ASP.NET uygulamasının ne kadar bellek kullandığını gösteren bir fikir almak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="a9829-143">Görev Yöneticisi bellek değeri:</span><span class="sxs-lookup"><span data-stu-id="a9829-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="a9829-144">ASP.NET işlemi tarafından kullanılan bellek miktarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="a9829-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="a9829-145">Uygulamanın oturma nesnelerini ve yerel bellek kullanımı gibi diğer bellek tüketicilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="a9829-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="a9829-146">Görev Yöneticisi bellek değeri sonsuza kadar artıyorsa ve hiçbir şekilde düzermez, uygulamanın bellek sızıntısı vardır.</span><span class="sxs-lookup"><span data-stu-id="a9829-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="a9829-147">Aşağıdaki bölümlerde, çeşitli bellek kullanımı desenleri gösterilmektedir ve açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a9829-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="a9829-148">Örnek görüntüleme belleği kullanım uygulaması</span><span class="sxs-lookup"><span data-stu-id="a9829-148">Sample display memory usage app</span></span>

<span data-ttu-id="a9829-149">[Memoryleak örnek uygulaması](https://github.com/sebastienros/memoryleak) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="a9829-150">MemoryLeak uygulaması:</span><span class="sxs-lookup"><span data-stu-id="a9829-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="a9829-151">, Uygulamanın gerçek zamanlı bellek ve GC verilerini toplayan bir tanılama denetleyicisi içerir.</span><span class="sxs-lookup"><span data-stu-id="a9829-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="a9829-152">, Bellek ve GC verilerini görüntüleyen bir dizin sayfasına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="a9829-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="a9829-153">Dizin sayfası her saniye yenilenir.</span><span class="sxs-lookup"><span data-stu-id="a9829-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="a9829-154">Çeşitli bellek yük desenleri sağlayan bir API denetleyicisi içerir.</span><span class="sxs-lookup"><span data-stu-id="a9829-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="a9829-155">Desteklenen bir araç değildir, ancak ASP.NET Core uygulamaların bellek kullanım düzenlerini göstermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="a9829-156">MemoryLeak 'yi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a9829-156">Run MemoryLeak.</span></span> <span data-ttu-id="a9829-157">Ayrılan bellek, GC gerçekleşene kadar yavaş artar.</span><span class="sxs-lookup"><span data-stu-id="a9829-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="a9829-158">Araç, verileri yakalamak için özel nesne ayırdığından bellek artar.</span><span class="sxs-lookup"><span data-stu-id="a9829-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="a9829-159">Aşağıdaki görüntüde bir gen 0 GC gerçekleştiğinde MemoryLeak Dizin sayfası gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="a9829-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="a9829-160">API denetleyicisinden API uç noktası çağrılmadığından grafik 0 RPS (saniye başına Istek) gösterir.</span><span class="sxs-lookup"><span data-stu-id="a9829-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![önceki grafik](memory/_static/0RPS.png)

<span data-ttu-id="a9829-162">Grafik, bellek kullanımı için iki değer görüntüler:</span><span class="sxs-lookup"><span data-stu-id="a9829-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="a9829-163">Ayrılan: yönetilen nesnelerin kapladığı bellek miktarı</span><span class="sxs-lookup"><span data-stu-id="a9829-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="a9829-164">[Çalışma kümesi](/windows/win32/memory/working-set): Şu anda fiziksel bellekte bulunan işlemin sanal adres alanındaki sayfa kümesi.</span><span class="sxs-lookup"><span data-stu-id="a9829-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="a9829-165">Gösterilen çalışma kümesi, Görev Yöneticisi 'nin görüntülediği değerdir.</span><span class="sxs-lookup"><span data-stu-id="a9829-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="a9829-166">Geçici nesneler</span><span class="sxs-lookup"><span data-stu-id="a9829-166">Transient objects</span></span>

<span data-ttu-id="a9829-167">Aşağıdaki API 10 KB 'lik bir dize örneği oluşturur ve istemciye döndürür.</span><span class="sxs-lookup"><span data-stu-id="a9829-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="a9829-168">Her istekte, bellekte yeni bir nesne ayrılır ve yanıta yazılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="a9829-169">Dizeler .NET 'te UTF-16 karakter olarak depolanır, böylece her karakter bellekte 2 bayt sürer.</span><span class="sxs-lookup"><span data-stu-id="a9829-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="a9829-170">Aşağıdaki grafik, ' de, bellek ayırmalarının GC tarafından nasıl etkilendiğini göstermek için görece küçük bir yük ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="a9829-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![önceki grafik](memory/_static/bigstring.png)

<span data-ttu-id="a9829-172">Yukarıdaki grafik şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="a9829-172">The preceding chart shows:</span></span>

* <span data-ttu-id="a9829-173">4K RPS (saniye başına Istek).</span><span class="sxs-lookup"><span data-stu-id="a9829-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="a9829-174">Nesil 0 GC koleksiyonları her iki saniyede bir gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="a9829-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="a9829-175">Çalışma kümesi yaklaşık 500 MB 'tan sabittir.</span><span class="sxs-lookup"><span data-stu-id="a9829-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="a9829-176">CPU %12 ' dir.</span><span class="sxs-lookup"><span data-stu-id="a9829-176">CPU is 12%.</span></span>
* <span data-ttu-id="a9829-177">Bellek tüketimi ve sürümü (GC aracılığıyla) kararlı bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="a9829-178">Aşağıdaki grafik, makine tarafından işlenebilen en fazla aktarım hızı üzerinden alınır.</span><span class="sxs-lookup"><span data-stu-id="a9829-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![önceki grafik](memory/_static/bigstring2.png)

<span data-ttu-id="a9829-180">Yukarıdaki grafik şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="a9829-180">The preceding chart shows:</span></span>

* <span data-ttu-id="a9829-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="a9829-181">22K RPS</span></span>
* <span data-ttu-id="a9829-182">Nesil 0 GC koleksiyonları saniye başına birkaç kez gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="a9829-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="a9829-183">1. nesil koleksiyonlar, uygulama saniye başına önemli ölçüde daha fazla bellek ayırdığından tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a9829-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="a9829-184">Çalışma kümesi yaklaşık 500 MB 'tan sabittir.</span><span class="sxs-lookup"><span data-stu-id="a9829-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="a9829-185">CPU %33 ' dir.</span><span class="sxs-lookup"><span data-stu-id="a9829-185">CPU is 33%.</span></span>
* <span data-ttu-id="a9829-186">Bellek tüketimi ve sürümü (GC aracılığıyla) kararlı bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="a9829-187">CPU (%33) aşırı kullanılmaz, bu nedenle çöp toplama işlemi yüksek sayıda ayırma ile devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="a9829-188">Workstation GC vs. Server GC</span><span class="sxs-lookup"><span data-stu-id="a9829-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="a9829-189">.NET atık toplayıcısı 'nda iki farklı mod vardır:</span><span class="sxs-lookup"><span data-stu-id="a9829-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="a9829-190">**Iş Istasyonu GC**: Masaüstü için iyileştirildi.</span><span class="sxs-lookup"><span data-stu-id="a9829-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="a9829-191">**Sunucu GC**.</span><span class="sxs-lookup"><span data-stu-id="a9829-191">**Server GC**.</span></span> <span data-ttu-id="a9829-192">ASP.NET Core uygulamalar için varsayılan GC.</span><span class="sxs-lookup"><span data-stu-id="a9829-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="a9829-193">Sunucu için iyileştirildi.</span><span class="sxs-lookup"><span data-stu-id="a9829-193">Optimized for the server.</span></span>

<span data-ttu-id="a9829-194">GC modu proje dosyasında veya yayımlanan uygulamanın *runtimeconfig. JSON* dosyasında açıkça ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="a9829-195">Aşağıdaki biçimlendirme proje dosyasındaki ayarı `ServerGarbageCollection` gösterir:</span><span class="sxs-lookup"><span data-stu-id="a9829-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="a9829-196">Proje `ServerGarbageCollection` dosyasında değiştirme uygulamanın yeniden oluşturulmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a9829-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="a9829-197">**Note:** Sunucu çöp toplama, tek çekirdekli **makinelerde kullanılamaz.**</span><span class="sxs-lookup"><span data-stu-id="a9829-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="a9829-198">Daha fazla bilgi için bkz. <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="a9829-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="a9829-199">Aşağıdaki görüntüde, Workstation GC kullanarak bir 5K RPS altındaki bellek profili gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="a9829-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![önceki grafik](memory/_static/workstation.png)

<span data-ttu-id="a9829-201">Bu grafik ve sunucu sürümü arasındaki farklar önemlidir:</span><span class="sxs-lookup"><span data-stu-id="a9829-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="a9829-202">Çalışma kümesi 500 MB ile 70 MB arasında bırakılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="a9829-203">GC, her iki saniyede yerine saniyede birden çok kez 1. nesil koleksiyonu yapar.</span><span class="sxs-lookup"><span data-stu-id="a9829-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="a9829-204">GC 300 MB 'den 10 MB 'a düşün.</span><span class="sxs-lookup"><span data-stu-id="a9829-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="a9829-205">Tipik bir Web sunucusu ortamında CPU kullanımı bellekten daha önemlidir, bu nedenle sunucu GC daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="a9829-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="a9829-206">Bellek kullanımı yüksekse ve CPU kullanımı nispeten düşükse, Iş Istasyonu GC daha iyi olabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="a9829-207">Örneğin, belleğin scarce olduğu çeşitli Web uygulamalarını barındıran yüksek yoğunluklu.</span><span class="sxs-lookup"><span data-stu-id="a9829-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="a9829-208">Docker ve küçük kapsayıcılar kullanılarak GC</span><span class="sxs-lookup"><span data-stu-id="a9829-208">GC using Docker and small containers</span></span>

<span data-ttu-id="a9829-209">Bir makinede birden çok Kapsayıcılı uygulama çalıştığında, Iş Istasyonu GC sunucu GC 'den daha preformant olabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="a9829-210">Daha fazla bilgi için, bkz. [bir küçük kapsayıcıda Server GC Ile çalıştırma](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) ve [küçük bir KAPSAYıCı senaryosunda sunucu GC ile bırlıkte çalıştırma-GC yığını için sabit sınır](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="a9829-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="a9829-211">Kalıcı nesne başvuruları</span><span class="sxs-lookup"><span data-stu-id="a9829-211">Persistent object references</span></span>

<span data-ttu-id="a9829-212">GC, başvurulan nesneleri serbest olamaz.</span><span class="sxs-lookup"><span data-stu-id="a9829-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="a9829-213">Başvurulan ancak artık gerekli olmayan nesneler bellek sızıntısına neden olur.</span><span class="sxs-lookup"><span data-stu-id="a9829-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="a9829-214">Uygulama genellikle nesneleri ayırırsa ve artık gerekli olmadıklarında bunları boşaltamazsa, bellek kullanımı zaman içinde artar.</span><span class="sxs-lookup"><span data-stu-id="a9829-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="a9829-215">Aşağıdaki API 10 KB 'lik bir dize örneği oluşturur ve istemciye döndürür.</span><span class="sxs-lookup"><span data-stu-id="a9829-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="a9829-216">Önceki örnekle aradaki fark, bu örneğe statik bir üye tarafından başvuruluyorsa, yani koleksiyon için hiçbir şekilde kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="a9829-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="a9829-217">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="a9829-217">The preceding code:</span></span>

* <span data-ttu-id="a9829-218">Tipik bir bellek sızıntısı örneğidir.</span><span class="sxs-lookup"><span data-stu-id="a9829-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="a9829-219">Sık yapılan çağrılar sayesinde, işlem bir `OutOfMemory` özel durumla çökene kadar uygulama belleğinin artmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="a9829-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![önceki grafik](memory/_static/eternal.png)

<span data-ttu-id="a9829-221">Önceki görüntüde:</span><span class="sxs-lookup"><span data-stu-id="a9829-221">In the preceding image:</span></span>

* <span data-ttu-id="a9829-222">Yük testi, `/api/staticstring` uç nokta bellekte doğrusal artışa neden olur.</span><span class="sxs-lookup"><span data-stu-id="a9829-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="a9829-223">GC, bellek baskısı arttıkça 2. nesil bir koleksiyon çağırarak belleği boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="a9829-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="a9829-224">GC, sızdırılan belleği serbest olamaz.</span><span class="sxs-lookup"><span data-stu-id="a9829-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="a9829-225">Ayrılan ve çalışma kümesi zaman ile artar.</span><span class="sxs-lookup"><span data-stu-id="a9829-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="a9829-226">Önbelleğe alma gibi bazı senaryolar, bellek baskısı serbest bırakılana kadar nesne başvurularının tutulmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a9829-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="a9829-227"><xref:System.WeakReference> Sınıfı bu tür bir önbelleğe alma kodu için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="a9829-228">Bir `WeakReference` nesne, bellek baskılarına altında toplanır.</span><span class="sxs-lookup"><span data-stu-id="a9829-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="a9829-229">Varsayılan <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> kullanım `WeakReference`uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="a9829-230">Yerel bellek</span><span class="sxs-lookup"><span data-stu-id="a9829-230">Native memory</span></span>

<span data-ttu-id="a9829-231">Bazı .NET Core nesneleri yerel belleğe bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="a9829-232">Yerel bellek GC tarafından **toplanamaz.**</span><span class="sxs-lookup"><span data-stu-id="a9829-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="a9829-233">Yerel bellek kullanan .NET nesnesi yerel kod kullanarak onu serbest vermelidir.</span><span class="sxs-lookup"><span data-stu-id="a9829-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="a9829-234">.NET, <xref:System.IDisposable> geliştiricilerin yerel bellek yayınlamasına izin vermek için arabirim sağlar.</span><span class="sxs-lookup"><span data-stu-id="a9829-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="a9829-235"><xref:System.IDisposable.Dispose*> Çağrılmasa bile, [Sonlandırıcı](/dotnet/csharp/programming-guide/classes-and-structs/destructors) çalıştırıldığında doğru uygulanmış sınıflar çağrısı `Dispose` .</span><span class="sxs-lookup"><span data-stu-id="a9829-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="a9829-236">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="a9829-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="a9829-237">[Physicalfileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) yönetilen bir sınıftır, bu nedenle isteğin sonunda herhangi bir örnek toplanacaktır.</span><span class="sxs-lookup"><span data-stu-id="a9829-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="a9829-238">Aşağıdaki görüntüde, `fileprovider` API 'yi sürekli çağırırken bellek profili gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="a9829-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![önceki grafik](memory/_static/fileprovider.png)

<span data-ttu-id="a9829-240">Yukarıdaki grafikte, bu sınıfın uygulanmasıyla ilgili olarak, bellek kullanımının artmasının devam eden belirgin bir sorun gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="a9829-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="a9829-241">Bu, [Bu sorun](https://github.com/dotnet/aspnetcore/issues/3110)içinde izlenmekte olan bilinen bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="a9829-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="a9829-242">Kullanıcı kodunda, aşağıdakilerden biri ile aynı sızıntı gerçekleşecektir:</span><span class="sxs-lookup"><span data-stu-id="a9829-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="a9829-243">Sınıf doğru şekilde serbest bırakılmıyor.</span><span class="sxs-lookup"><span data-stu-id="a9829-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="a9829-244">Atılan bağımlı nesnelerin `Dispose`yöntemini çağırmak için foralıtıon.</span><span class="sxs-lookup"><span data-stu-id="a9829-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="a9829-245">Büyük nesne yığını</span><span class="sxs-lookup"><span data-stu-id="a9829-245">Large objects heap</span></span>

<span data-ttu-id="a9829-246">Sık bellek ayırma/ücretsiz döngüler, özellikle büyük bellek öbekleri ayrılırken belleği parçalara ayırıyor.</span><span class="sxs-lookup"><span data-stu-id="a9829-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="a9829-247">Nesneler bitişik bellek bloklarına ayrılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="a9829-248">Parçalanmayı azaltmak için, GC belleği serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="a9829-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="a9829-249">Bu işleme **sıkıştırma**adı verilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-249">This process is called **compaction**.</span></span> <span data-ttu-id="a9829-250">Sıkıştırma, nesneleri taşımayı içerir.</span><span class="sxs-lookup"><span data-stu-id="a9829-250">Compaction involves moving objects.</span></span> <span data-ttu-id="a9829-251">Büyük nesnelerin taşınması, bir performans cezası getirir.</span><span class="sxs-lookup"><span data-stu-id="a9829-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="a9829-252">Bu nedenle GC, büyük [nesne yığını](/dotnet/standard/garbage-collection/large-object-heap) (LOH) olarak adlandırılan _büyük_ nesneler için özel bir bellek bölgesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a9829-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="a9829-253">85.000 bayttan (yaklaşık 83 KB) büyük olan nesneler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a9829-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="a9829-254">LOH 'ye yerleştirildi.</span><span class="sxs-lookup"><span data-stu-id="a9829-254">Placed on the LOH.</span></span>
* <span data-ttu-id="a9829-255">Düzenlenmedi.</span><span class="sxs-lookup"><span data-stu-id="a9829-255">Not compacted.</span></span>
* <span data-ttu-id="a9829-256">2. nesil oluşturma sırasında toplanır.</span><span class="sxs-lookup"><span data-stu-id="a9829-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="a9829-257">LOH dolduğunda GC, 2. nesil bir koleksiyon tetikleyecektir.</span><span class="sxs-lookup"><span data-stu-id="a9829-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="a9829-258">2. nesil Koleksiyonlar:</span><span class="sxs-lookup"><span data-stu-id="a9829-258">Generation 2 collections:</span></span>

* <span data-ttu-id="a9829-259">Doğal olarak yavaştır.</span><span class="sxs-lookup"><span data-stu-id="a9829-259">Are inherently slow.</span></span>
* <span data-ttu-id="a9829-260">Ayrıca, diğer tüm neslerde bir koleksiyonun tetiklenmesi için de ücret uygulanır.</span><span class="sxs-lookup"><span data-stu-id="a9829-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="a9829-261">Aşağıdaki kod, LOH 'yi hemen sıkıştırır:</span><span class="sxs-lookup"><span data-stu-id="a9829-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="a9829-262">LOH 'yi düzenleme hakkında bilgi için bkz <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> ..</span><span class="sxs-lookup"><span data-stu-id="a9829-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="a9829-263">.NET Core 3,0 ve üstünü kullanan kapsayıcılarda LOH otomatik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="a9829-264">Bu davranışı gösteren aşağıdaki API:</span><span class="sxs-lookup"><span data-stu-id="a9829-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="a9829-265">Aşağıdaki grafikte, en fazla yük altında `/api/loh/84975` uç nokta çağırma bellek profili gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="a9829-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![önceki grafik](memory/_static/loh1.png)

<span data-ttu-id="a9829-267">Aşağıdaki grafik, `/api/loh/84976` uç noktayı çağırmanın bellek profilini gösterir ve *yalnızca bir bayt daha*ayırarak aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="a9829-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![önceki grafik](memory/_static/loh2.png)

<span data-ttu-id="a9829-269">Note: `byte[]` yapıda ek yük baytları vardır.</span><span class="sxs-lookup"><span data-stu-id="a9829-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="a9829-270">Bu nedenle 84.976 bayt 85.000 limitini tetikler.</span><span class="sxs-lookup"><span data-stu-id="a9829-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="a9829-271">Önceki iki grafik karşılaştırılıyor:</span><span class="sxs-lookup"><span data-stu-id="a9829-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="a9829-272">Çalışma kümesi, yaklaşık 450 MB olmak üzere her iki senaryo için de benzerdir.</span><span class="sxs-lookup"><span data-stu-id="a9829-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="a9829-273">Altındaki LOH istekleri (84.975 bayt), çoğunlukla nesil 0 koleksiyonlarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="a9829-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="a9829-274">Over LOH istekleri, sabit 2. nesil koleksiyonlar üretir.</span><span class="sxs-lookup"><span data-stu-id="a9829-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="a9829-275">2. nesil koleksiyonlar pahalıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="a9829-276">Daha fazla CPU gerekir ve verimlilik neredeyse %50 ' a düşyordu.</span><span class="sxs-lookup"><span data-stu-id="a9829-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="a9829-277">Geçici büyük nesneler özellikle sorunlu olduğundan Gen2 GCs 'ye neden olur.</span><span class="sxs-lookup"><span data-stu-id="a9829-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="a9829-278">En yüksek performans için büyük nesne kullanımı küçültülmüş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="a9829-279">Mümkünse, büyük nesneleri ayırın.</span><span class="sxs-lookup"><span data-stu-id="a9829-279">If possible, split up large objects.</span></span> <span data-ttu-id="a9829-280">Örneğin, [yanıt önbelleğe alma](xref:performance/caching/response) ara yazılımı ASP.NET Core önbellek girişlerini 85.000 bayttan daha az blok halinde ayırır.</span><span class="sxs-lookup"><span data-stu-id="a9829-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="a9829-281">Aşağıdaki bağlantılarda, LOH sınırı altına nesneleri tutma ASP.NET Core yaklaşımı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="a9829-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="a9829-282">ResponseCaching/Streams/Streammutilities. cs</span><span class="sxs-lookup"><span data-stu-id="a9829-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="a9829-283">ResponseCaching/MemoryResponseCache. cs</span><span class="sxs-lookup"><span data-stu-id="a9829-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="a9829-284">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="a9829-284">For more information, see:</span></span>

* [<span data-ttu-id="a9829-285">Büyük nesne yığını kapsanmamış</span><span class="sxs-lookup"><span data-stu-id="a9829-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="a9829-286">Büyük nesne yığını</span><span class="sxs-lookup"><span data-stu-id="a9829-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="a9829-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="a9829-287">HttpClient</span></span>

<span data-ttu-id="a9829-288">Yanlış kullanımı <xref:System.Net.Http.HttpClient> , kaynak sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="a9829-289">Veritabanı bağlantıları, yuvalar, dosya tutamaçları vb. gibi sistem kaynakları:</span><span class="sxs-lookup"><span data-stu-id="a9829-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="a9829-290">, Bellekten daha fazla.</span><span class="sxs-lookup"><span data-stu-id="a9829-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="a9829-291">Bellekten sızmış olduğunda daha sorunlu sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="a9829-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="a9829-292">Deneyimli .NET geliştiricileri, uygulayan <xref:System.IDisposable.Dispose*> <xref:System.IDisposable>nesneler üzerinde arama gerektiğini bilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="a9829-293">`IDisposable` Genellikle, elde edilen nesneleri atan bellek ya da sızdırılan sistem kaynakları ile ilgili olarak elden atma.</span><span class="sxs-lookup"><span data-stu-id="a9829-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="a9829-294">`HttpClient`uygular `IDisposable`, ancak her çağrıdan **çıkarılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="a9829-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="a9829-295">Bunun yerine `HttpClient` yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="a9829-296">Aşağıdaki uç nokta her istekte yeni `HttpClient` bir örnek oluşturur ve atar:</span><span class="sxs-lookup"><span data-stu-id="a9829-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="a9829-297">Yük altında aşağıdaki hata iletileri günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a9829-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="a9829-298">`HttpClient` Örnekler atılsa da, gerçek ağ bağlantısının işletim sistemi tarafından yayımlanması zaman alır.</span><span class="sxs-lookup"><span data-stu-id="a9829-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="a9829-299">Sürekli olarak yeni bağlantılar oluşturarak _bağlantı noktaları tükenmesi_ oluşur.</span><span class="sxs-lookup"><span data-stu-id="a9829-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="a9829-300">Her istemci bağlantısı kendi istemci bağlantı noktasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a9829-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="a9829-301">Bağlantı noktası tükenmesi 'ni önlemenin bir yolu aynı `HttpClient` örneği yeniden kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="a9829-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="a9829-302">Uygulama `HttpClient` durdurulduğunda örnek serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="a9829-303">Bu örnek her bir atılabilir kaynağının her bir kullanım sonrasında atılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="a9829-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="a9829-304">Bir `HttpClient` örneğin yaşam süresini işlemenin daha iyi bir yolu için aşağıdakilere bakın:</span><span class="sxs-lookup"><span data-stu-id="a9829-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="a9829-305">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="a9829-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="a9829-306">HTTPClient Factory blogu</span><span class="sxs-lookup"><span data-stu-id="a9829-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="a9829-307">Nesne havuzu oluşturma</span><span class="sxs-lookup"><span data-stu-id="a9829-307">Object pooling</span></span>

<span data-ttu-id="a9829-308">Önceki örnek, `HttpClient` örneğin tüm istekler tarafından nasıl statik hale getirilebilir ve yeniden kullanılabileceğini gösterdi.</span><span class="sxs-lookup"><span data-stu-id="a9829-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="a9829-309">Yeniden kullanım, kaynak dışı çalışmayı önler.</span><span class="sxs-lookup"><span data-stu-id="a9829-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="a9829-310">Nesne havuzu:</span><span class="sxs-lookup"><span data-stu-id="a9829-310">Object pooling:</span></span>

* <span data-ttu-id="a9829-311">Yeniden kullanım modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="a9829-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="a9829-312">, Oluşturulması pahalı olan nesneler için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="a9829-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="a9829-313">Havuz, iş parçacıkları genelinde ayrılmaları ve yayımlanmaları önceden başlatılmış nesnelerden oluşan bir koleksiyondur.</span><span class="sxs-lookup"><span data-stu-id="a9829-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="a9829-314">Havuzlar sınırlar, önceden tanımlanmış boyutlar veya büyüme oranı gibi ayırma kuralları tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="a9829-315">[Microsoft. Extensions. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) NuGet paketi, bu tür havuzları yönetmeye yardımcı olan sınıflar içerir.</span><span class="sxs-lookup"><span data-stu-id="a9829-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="a9829-316">Aşağıdaki API uç noktası her istekte `byte` rastgele sayılarla doldurulmuş bir arabellek başlatır:</span><span class="sxs-lookup"><span data-stu-id="a9829-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="a9829-317">Aşağıdaki grafik, önceki API 'nin orta yük ile çağrılmasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="a9829-317">The following chart display calling the preceding API with moderate load:</span></span>

![önceki grafik](memory/_static/array.png)

<span data-ttu-id="a9829-319">Yukarıdaki grafikte, nesil 0 toplamaları yaklaşık olarak saniyede bir kez gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="a9829-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="a9829-320">Önceki kod, `byte` [arraypool\<T>](xref:System.Buffers.ArrayPool`1)kullanılarak arabelleği havuzlayarak iyileştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="a9829-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="a9829-321">Bir statik örnek istekler arasında yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="a9829-322">Bu yaklaşımla farklı olan özellikler, havuza alınmış bir nesnenin API 'den döndürüldüğü şeydir.</span><span class="sxs-lookup"><span data-stu-id="a9829-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="a9829-323">Bunun anlamı:</span><span class="sxs-lookup"><span data-stu-id="a9829-323">That means:</span></span>

* <span data-ttu-id="a9829-324">Yöntemi, yönteminden geri döndükten hemen sonra Denetim dışındadır.</span><span class="sxs-lookup"><span data-stu-id="a9829-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="a9829-325">Nesneyi serbest bırakamıyoruz.</span><span class="sxs-lookup"><span data-stu-id="a9829-325">You can't release the object.</span></span>

<span data-ttu-id="a9829-326">Nesnenin elden çıkarılmasını ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="a9829-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="a9829-327">Havuza alınmış diziyi bir atılabilir nesnesinde yalıtma.</span><span class="sxs-lookup"><span data-stu-id="a9829-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="a9829-328">Havuza alınmış nesneyi [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a9829-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="a9829-329">`RegisterForDispose`, yalnızca HTTP isteği tamamlandığında `Dispose`serbest bırakılacak şekilde hedef nesneye çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="a9829-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="a9829-330">Havuza alınmamış sürüm olarak aynı yükün uygulanması aşağıdaki grafiğe neden olur:</span><span class="sxs-lookup"><span data-stu-id="a9829-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![önceki grafik](memory/_static/pooledarray.png)

<span data-ttu-id="a9829-332">Ana fark ayrılan bayttır ve çok daha az nesil 0 koleksiyonu olarak.</span><span class="sxs-lookup"><span data-stu-id="a9829-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9829-333">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a9829-333">Additional resources</span></span>

* [<span data-ttu-id="a9829-334">Çöp toplama</span><span class="sxs-lookup"><span data-stu-id="a9829-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="a9829-335">Eşzamanlılık görselleştiricisi ile farklı GC modlarını anlama</span><span class="sxs-lookup"><span data-stu-id="a9829-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="a9829-336">Büyük nesne yığını kapsanmamış</span><span class="sxs-lookup"><span data-stu-id="a9829-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="a9829-337">Büyük nesne yığını</span><span class="sxs-lookup"><span data-stu-id="a9829-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
