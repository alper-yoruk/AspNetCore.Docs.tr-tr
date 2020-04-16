---
title: ASP.NET Core'da bellek yönetimi ve desenler
author: rick-anderson
description: ASP.NET Core'da belleğin nasıl yönetildiğini ve çöp toplayıcının (GC) nasıl çalıştığını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440954"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="50602-103">ASP.NET Core'da bellek yönetimi ve çöp toplama (GC)</span><span class="sxs-lookup"><span data-stu-id="50602-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="50602-104">Yazar: [Sébastien Ros](https://github.com/sebastienros) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="50602-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="50602-105">Bellek yönetimi,.NET gibi yönetilen bir çerçevede bile karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="50602-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="50602-106">Bellek sorunlarını analiz etmek ve anlamak zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="50602-107">Bu makalede:</span><span class="sxs-lookup"><span data-stu-id="50602-107">This article:</span></span>

* <span data-ttu-id="50602-108">Birçok bellek *sızıntısı* ve *GC sorunları çalışmıyor* tarafından motive edildi.</span><span class="sxs-lookup"><span data-stu-id="50602-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="50602-109">Bu sorunların çoğu, bellek tüketiminin .NET Core'da nasıl çalıştığını anlamamak veya nasıl ölçüldüğünü anlamamak nedeniyle kaynaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="50602-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="50602-110">Sorunlu bellek kullanımını gösterir ve alternatif yaklaşımlar önerir.</span><span class="sxs-lookup"><span data-stu-id="50602-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="50602-111">.NET Core'da çöp toplama (GC) nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="50602-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="50602-112">GC, her kesimin bitişik bir bellek aralığı olduğu yığın segmentler ayırır.</span><span class="sxs-lookup"><span data-stu-id="50602-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="50602-113">Yığına yerleştirilen nesneler 3 nesilden birine ayrılır: 0, 1 veya 2.</span><span class="sxs-lookup"><span data-stu-id="50602-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="50602-114">Oluşturma, GC'nin artık uygulama tarafından başvurulan olmayan yönetilen nesnelerde bellek serbest bırakmaya çalıştığı sıklığını belirler.</span><span class="sxs-lookup"><span data-stu-id="50602-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="50602-115">Daha düşük numaralı nesiller gc'd daha sık vardır.</span><span class="sxs-lookup"><span data-stu-id="50602-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="50602-116">Nesneler yaşamlarına göre bir nesilden diğerine taşınır.</span><span class="sxs-lookup"><span data-stu-id="50602-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="50602-117">Nesneler daha uzun yaşadıkça, daha yüksek bir nesle taşınırlar.</span><span class="sxs-lookup"><span data-stu-id="50602-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="50602-118">Daha önce de belirtildiği gibi, yüksek nesiller gc'd daha az sıklıkta.</span><span class="sxs-lookup"><span data-stu-id="50602-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="50602-119">Kısa süreli yaşayan nesneler her zaman nesil 0 kalır.</span><span class="sxs-lookup"><span data-stu-id="50602-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="50602-120">Örneğin, bir web isteğinin ömrü boyunca başvurulan nesneler kısa ömürlüdür.</span><span class="sxs-lookup"><span data-stu-id="50602-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="50602-121">Uygulama düzeyi [singletons](xref:fundamentals/dependency-injection#service-lifetimes) genellikle nesil 2'ye göç.</span><span class="sxs-lookup"><span data-stu-id="50602-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="50602-122">bir ASP.NET Core uygulaması başladığında, GC:</span><span class="sxs-lookup"><span data-stu-id="50602-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="50602-123">İlk yığın segmentleri için bazı bellek yedekler.</span><span class="sxs-lookup"><span data-stu-id="50602-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="50602-124">Çalışma zamanı yüklendiğinde belleğin küçük bir bölümünü işler.</span><span class="sxs-lookup"><span data-stu-id="50602-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="50602-125">Önceki bellek ayırmaları performans nedenleriyle yapılır.</span><span class="sxs-lookup"><span data-stu-id="50602-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="50602-126">Performans avantajı bitişik bellekteki yığın segmentlerden gelir.</span><span class="sxs-lookup"><span data-stu-id="50602-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="50602-127">GC'yi ara. Toplamak</span><span class="sxs-lookup"><span data-stu-id="50602-127">Call GC.Collect</span></span>

<span data-ttu-id="50602-128">[GC'yi arıyorum. Açıkça toplayın:](xref:System.GC.Collect*)</span><span class="sxs-lookup"><span data-stu-id="50602-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="50602-129">Core uygulamaları nın üretim ASP.NET **yapılmaması** gerekir.</span><span class="sxs-lookup"><span data-stu-id="50602-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="50602-130">Bellek sızıntılarını araştırırken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="50602-131">Araştırma yaparken, GC bellek ölçülebilir böylece bellekten tüm sarkan nesneleri kaldırdı doğrular.</span><span class="sxs-lookup"><span data-stu-id="50602-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="50602-132">Bir uygulamanın bellek kullanımını analiz etme</span><span class="sxs-lookup"><span data-stu-id="50602-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="50602-133">Özel araçlar bellek kullanımını analiz yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="50602-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="50602-134">Nesne başvurularını sayma</span><span class="sxs-lookup"><span data-stu-id="50602-134">Counting object references</span></span>
- <span data-ttu-id="50602-135">GC'nin CPU kullanımı üzerinde ne kadar etkisi olduğunu ölçme</span><span class="sxs-lookup"><span data-stu-id="50602-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="50602-136">Her nesil için kullanılan bellek alanını ölçme</span><span class="sxs-lookup"><span data-stu-id="50602-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="50602-137">Bellek kullanımını analiz etmek için aşağıdaki araçları kullanın:</span><span class="sxs-lookup"><span data-stu-id="50602-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="50602-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Üretim makinelerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="50602-139">Visual Studio hata ayıklama olmadan bellek kullanımını analiz edin</span><span class="sxs-lookup"><span data-stu-id="50602-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="50602-140">Visual Studio’da bellek kullanımının profilini oluşturma</span><span class="sxs-lookup"><span data-stu-id="50602-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="50602-141">Bellek sorunlarını algılama</span><span class="sxs-lookup"><span data-stu-id="50602-141">Detecting memory issues</span></span>

<span data-ttu-id="50602-142">Görev Yöneticisi, bir ASP.NET uygulamasının ne kadar bellek kullandığı hakkında bir fikir almak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="50602-143">Görev Yöneticisi bellek değeri:</span><span class="sxs-lookup"><span data-stu-id="50602-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="50602-144">ASP.NET işlemi tarafından kullanılan bellek miktarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="50602-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="50602-145">Uygulamanın canlı nesnelerini ve yerel bellek kullanımı gibi diğer bellek tüketicilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="50602-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="50602-146">Görev Yöneticisi bellek değeri süresiz olarak artar ve hiçbir zaman düzleşirse, uygulamanın bir bellek sızıntısı vardır.</span><span class="sxs-lookup"><span data-stu-id="50602-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="50602-147">Aşağıdaki bölümlerde çeşitli bellek kullanım desenleri gösterin ve açıklar.</span><span class="sxs-lookup"><span data-stu-id="50602-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="50602-148">Örnek ekran bellek kullanım uygulaması</span><span class="sxs-lookup"><span data-stu-id="50602-148">Sample display memory usage app</span></span>

<span data-ttu-id="50602-149">[MemoryLeak örnek uygulaması](https://github.com/sebastienros/memoryleak) GitHub'da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="50602-150">MemoryLeak uygulaması:</span><span class="sxs-lookup"><span data-stu-id="50602-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="50602-151">Uygulama için gerçek zamanlı bellek ve GC verilerini toplayan bir tanıdenetleyicisi içerir.</span><span class="sxs-lookup"><span data-stu-id="50602-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="50602-152">Bellek ve GC verilerini görüntüleyen bir Dizin sayfası vardır.</span><span class="sxs-lookup"><span data-stu-id="50602-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="50602-153">Dizin sayfası her saniye yenilenir.</span><span class="sxs-lookup"><span data-stu-id="50602-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="50602-154">Çeşitli bellek yükü desenleri sağlayan bir API denetleyicisi içerir.</span><span class="sxs-lookup"><span data-stu-id="50602-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="50602-155">Desteklenen bir araç değildir, ancak ASP.NET Core uygulamalarıbellek kullanım desenleri görüntülemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="50602-156">MemoryLeak çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="50602-156">Run MemoryLeak.</span></span> <span data-ttu-id="50602-157">Ayrılan bellek, GC oluşana kadar yavaşça artar.</span><span class="sxs-lookup"><span data-stu-id="50602-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="50602-158">Araç veri yakalamak için özel nesne ayırdığından bellek artar.</span><span class="sxs-lookup"><span data-stu-id="50602-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="50602-159">Aşağıdaki resim, Gen 0 GC oluştuğunda MemoryLeak Index sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="50602-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="50602-160">GRAFIK, API denetleyicisinden hiçbir API uç noktası çağrıldığından, 0 RPS (Saniyede istek) gösterir.</span><span class="sxs-lookup"><span data-stu-id="50602-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![önceki grafik](memory/_static/0RPS.png)

<span data-ttu-id="50602-162">Grafik bellek kullanımı için iki değer görüntüler:</span><span class="sxs-lookup"><span data-stu-id="50602-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="50602-163">Ayrılan: yönetilen nesneler tarafından işgal edilen bellek miktarı</span><span class="sxs-lookup"><span data-stu-id="50602-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="50602-164">[Çalışma kümesi](/windows/win32/memory/working-set): Şu anda fiziksel bellekte ikamet eden işlemin sanal adres alanında yer alan sayfa kümesi.</span><span class="sxs-lookup"><span data-stu-id="50602-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="50602-165">Gösterilen çalışma kümesi, Görev Yöneticisi'nin gösterdiği değerle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="50602-166">Geçici nesneler</span><span class="sxs-lookup"><span data-stu-id="50602-166">Transient objects</span></span>

<span data-ttu-id="50602-167">Aşağıdaki API 10-KB String örneği oluşturur ve istemciye döndürür.</span><span class="sxs-lookup"><span data-stu-id="50602-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="50602-168">Her istekte, bellekte yeni bir nesne ayrılır ve yanıta yazılır.</span><span class="sxs-lookup"><span data-stu-id="50602-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="50602-169">Dizeleri .NET'te UTF-16 karakteri olarak depolanır, böylece her karakter bellekte 2 bayt alır.</span><span class="sxs-lookup"><span data-stu-id="50602-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="50602-170">Aşağıdaki grafik, bellek ayırmalarının GC tarafından nasıl etkilendiğini göstermek için nispeten küçük bir yükle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="50602-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![önceki grafik](memory/_static/bigstring.png)

<span data-ttu-id="50602-172">Önceki grafik tesniye:</span><span class="sxs-lookup"><span data-stu-id="50602-172">The preceding chart shows:</span></span>

* <span data-ttu-id="50602-173">4K RPS (İstekler saniyede).</span><span class="sxs-lookup"><span data-stu-id="50602-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="50602-174">Nesil 0 GC koleksiyonları yaklaşık her iki saniyede bir oluşur.</span><span class="sxs-lookup"><span data-stu-id="50602-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="50602-175">Çalışma seti yaklaşık 500 MB sabittir.</span><span class="sxs-lookup"><span data-stu-id="50602-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="50602-176">CPU%12'dir.</span><span class="sxs-lookup"><span data-stu-id="50602-176">CPU is 12%.</span></span>
* <span data-ttu-id="50602-177">Bellek tüketimi ve serbest bırakılması (GC ile) kararlıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="50602-178">Aşağıdaki grafik, makine tarafından işlenebilen maksimum iş lenme noktasında alınır.</span><span class="sxs-lookup"><span data-stu-id="50602-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![önceki grafik](memory/_static/bigstring2.png)

<span data-ttu-id="50602-180">Önceki grafik tesniye:</span><span class="sxs-lookup"><span data-stu-id="50602-180">The preceding chart shows:</span></span>

* <span data-ttu-id="50602-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="50602-181">22K RPS</span></span>
* <span data-ttu-id="50602-182">Nesil 0 GC koleksiyonları saniyede birkaç kez oluşur.</span><span class="sxs-lookup"><span data-stu-id="50602-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="50602-183">Uygulama saniyede önemli ölçüde daha fazla bellek ayırdığı için nesil 1 koleksiyonları tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="50602-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="50602-184">Çalışma seti yaklaşık 500 MB sabittir.</span><span class="sxs-lookup"><span data-stu-id="50602-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="50602-185">CPU %33'tür.</span><span class="sxs-lookup"><span data-stu-id="50602-185">CPU is 33%.</span></span>
* <span data-ttu-id="50602-186">Bellek tüketimi ve serbest bırakılması (GC ile) kararlıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="50602-187">CPU (%33) aşırı kullanılmadığından, çöp toplama çok sayıda ayırmaya yetişebilir.</span><span class="sxs-lookup"><span data-stu-id="50602-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="50602-188">İş İstasyonu GC vs Sunucu GC</span><span class="sxs-lookup"><span data-stu-id="50602-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="50602-189">.NET Çöp Toplayıcı'nın iki farklı modu vardır:</span><span class="sxs-lookup"><span data-stu-id="50602-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="50602-190">**Workstation GC**: Masaüstü için optimize edin.</span><span class="sxs-lookup"><span data-stu-id="50602-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="50602-191">**Sunucu GC**.</span><span class="sxs-lookup"><span data-stu-id="50602-191">**Server GC**.</span></span> <span data-ttu-id="50602-192">ASP.NET Core uygulamaları için varsayılan GC.</span><span class="sxs-lookup"><span data-stu-id="50602-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="50602-193">Sunucu için optimize edin.</span><span class="sxs-lookup"><span data-stu-id="50602-193">Optimized for the server.</span></span>

<span data-ttu-id="50602-194">GC modu, proje dosyasında veya yayınlanan uygulamanın *runtimeconfig.json* dosyasında açıkça ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="50602-195">Aşağıdaki biçimlendirme proje `ServerGarbageCollection` dosyasındaki ayarı gösterir:</span><span class="sxs-lookup"><span data-stu-id="50602-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="50602-196">Proje `ServerGarbageCollection` dosyasında yapılan değiştirme, uygulamanın yeniden oluşturulmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="50602-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="50602-197">**Not:** Sunucu çöp toplama tek bir çekirdekli makinelerde **kullanılamaz.**</span><span class="sxs-lookup"><span data-stu-id="50602-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="50602-198">Daha fazla bilgi için bkz. <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="50602-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="50602-199">Aşağıdaki resimde Workstation GC kullanarak bir 5K RPS altında bellek profili gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="50602-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![önceki grafik](memory/_static/workstation.png)

<span data-ttu-id="50602-201">Bu grafik ve sunucu sürümü arasındaki farklar önemlidir:</span><span class="sxs-lookup"><span data-stu-id="50602-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="50602-202">Çalışma seti 500 MB'dan 70 MB'a düşer.</span><span class="sxs-lookup"><span data-stu-id="50602-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="50602-203">GC, her iki saniyede bir değil, saniyede birden çok kez nesil 0 koleksiyonu yapar.</span><span class="sxs-lookup"><span data-stu-id="50602-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="50602-204">GC 300 MB'dan 10 MB'a düşer.</span><span class="sxs-lookup"><span data-stu-id="50602-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="50602-205">Tipik bir web sunucusu ortamında, CPU kullanımı bellekten daha önemlidir, bu nedenle Server GC daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="50602-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="50602-206">Bellek kullanımı yüksekse ve CPU kullanımı nispeten düşükse, Workstation GC daha fazla performans olabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="50602-207">Örneğin, bellek kıt olduğu çeşitli web uygulamaları barındırma yüksek yoğunluklu.</span><span class="sxs-lookup"><span data-stu-id="50602-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="50602-208">Docker ve küçük kaplar kullanarak GC</span><span class="sxs-lookup"><span data-stu-id="50602-208">GC using Docker and small containers</span></span>

<span data-ttu-id="50602-209">Tek bir makinede birden çok kapsayıcı uygulama çalışırken, Workstation GC Server GC'den daha preformant olabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="50602-210">Daha fazla bilgi için, [Küçük Bir Kapsayıcı da Server GC ile çalışan](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) ve Küçük Bir Kapsayıcı Senaryo Bölüm 1 Sunucu GC ile çalışan - [GC Yığını için Sabit Limit](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)bakın.</span><span class="sxs-lookup"><span data-stu-id="50602-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="50602-211">Kalıcı nesne başvuruları</span><span class="sxs-lookup"><span data-stu-id="50602-211">Persistent object references</span></span>

<span data-ttu-id="50602-212">GC başvurulan nesneleri serbest kılamaz.</span><span class="sxs-lookup"><span data-stu-id="50602-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="50602-213">Başvurulan ancak artık gerekk olmayan nesneler bellek sızıntısına neden olur.</span><span class="sxs-lookup"><span data-stu-id="50602-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="50602-214">Uygulama sık sık nesneleri ayırır ve artık ihtiyaç duyulmadıktan sonra bunları serbest bırakmazsa, bellek kullanımı zaman içinde artar.</span><span class="sxs-lookup"><span data-stu-id="50602-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="50602-215">Aşağıdaki API 10-KB String örneği oluşturur ve istemciye döndürür.</span><span class="sxs-lookup"><span data-stu-id="50602-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="50602-216">Önceki örnekteki fark, bu örneğin statik bir üye tarafından başvurulsa da, bu da hiçbir zaman koleksiyon için kullanılamadığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="50602-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="50602-217">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="50602-217">The preceding code:</span></span>

* <span data-ttu-id="50602-218">Tipik bir bellek sızıntısı örneğidir.</span><span class="sxs-lookup"><span data-stu-id="50602-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="50602-219">Sık yapılan aramalarda, işlem bir `OutOfMemory` istisna dışında çökene kadar uygulama belleği nin artmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="50602-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![önceki grafik](memory/_static/eternal.png)

<span data-ttu-id="50602-221">Önceki resimde:</span><span class="sxs-lookup"><span data-stu-id="50602-221">In the preceding image:</span></span>

* <span data-ttu-id="50602-222">Uç noktayı `/api/staticstring` test eden yük bellekte doğrusal bir artışa neden olur.</span><span class="sxs-lookup"><span data-stu-id="50602-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="50602-223">GC, bellek basıncı arttıkça, nesil 2 koleksiyonunu arayarak belleği serbest tutmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="50602-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="50602-224">GC, sızdırılan belleği kurtaramaz.</span><span class="sxs-lookup"><span data-stu-id="50602-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="50602-225">Tahsis ve çalışma seti zamanla artar.</span><span class="sxs-lookup"><span data-stu-id="50602-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="50602-226">Önbelleğe alma gibi bazı senaryolar, bellek basıncı onları serbest bırakılmaya zorlayana kadar nesne başvurularının tutulmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="50602-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="50602-227">Sınıf <xref:System.WeakReference> önbelleğe alma kodu bu tür için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="50602-228">Bir `WeakReference` nesne bellek basınçları altında toplanır.</span><span class="sxs-lookup"><span data-stu-id="50602-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="50602-229">Kullanımların <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> varsayılan `WeakReference`uygulaması.</span><span class="sxs-lookup"><span data-stu-id="50602-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="50602-230">Yerel bellek</span><span class="sxs-lookup"><span data-stu-id="50602-230">Native memory</span></span>

<span data-ttu-id="50602-231">Bazı .NET Core nesneleri yerel belleğe dayanır.</span><span class="sxs-lookup"><span data-stu-id="50602-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="50602-232">Yerel bellek GC tarafından **toplanabilir.**</span><span class="sxs-lookup"><span data-stu-id="50602-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="50602-233">Yerel belleği kullanan .NET nesnesi, yerel kodu kullanarak onu serbest etmelidir.</span><span class="sxs-lookup"><span data-stu-id="50602-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="50602-234">.NET, <xref:System.IDisposable> geliştiricilerin yerel belleği serbest bırakmasına izin veren arabirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="50602-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="50602-235"><xref:System.IDisposable.Dispose*> Çağrılmasa bile, doğru uygulanan `Dispose` sınıflar [sonlandırıcı](/dotnet/csharp/programming-guide/classes-and-structs/destructors) çalıştığında çağırır.</span><span class="sxs-lookup"><span data-stu-id="50602-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="50602-236">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="50602-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="50602-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) yönetilen bir sınıftır, bu nedenle herhangi bir örnek isteğin sonunda toplanır.</span><span class="sxs-lookup"><span data-stu-id="50602-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="50602-238">Aşağıdaki resim, `fileprovider` API'yi sürekli olarak çağırırken bellek profilini gösterir.</span><span class="sxs-lookup"><span data-stu-id="50602-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![önceki grafik](memory/_static/fileprovider.png)

<span data-ttu-id="50602-240">Önceki grafik, bellek kullanımını artırmaya devam ettikçe, bu sınıfın uygulanmasıyla ilgili bariz bir sorun gösterir.</span><span class="sxs-lookup"><span data-stu-id="50602-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="50602-241">Bu, [bu sorunda](https://github.com/dotnet/aspnetcore/issues/3110)izlenen bilinen bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="50602-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="50602-242">Aynı sızıntı aşağıdakilerden biri tarafından, kullanıcı kodunda olabilir:</span><span class="sxs-lookup"><span data-stu-id="50602-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="50602-243">Sınıfı doğru bırakmamak.</span><span class="sxs-lookup"><span data-stu-id="50602-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="50602-244">Atılması gereken bağımlı `Dispose`nesnelerin yöntemini çağırmayı unutma.</span><span class="sxs-lookup"><span data-stu-id="50602-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="50602-245">Büyük nesneler yığını</span><span class="sxs-lookup"><span data-stu-id="50602-245">Large objects heap</span></span>

<span data-ttu-id="50602-246">Sık bellek ayırma/boş döngüleri, özellikle büyük bellek parçaları ayırırken belleği parçalayabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="50602-247">Nesneler bitişik bellek bloklarında ayrılır.</span><span class="sxs-lookup"><span data-stu-id="50602-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="50602-248">Parçalanmayı azaltmak için, GC belleği serbest ettiğinde, parçalamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="50602-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="50602-249">Bu işleme **sıkıştırma**denir.</span><span class="sxs-lookup"><span data-stu-id="50602-249">This process is called **compaction**.</span></span> <span data-ttu-id="50602-250">Sıkıştırma nesneleri hareketli içerir.</span><span class="sxs-lookup"><span data-stu-id="50602-250">Compaction involves moving objects.</span></span> <span data-ttu-id="50602-251">Büyük nesneleri taşımak bir performans cezası uygular.</span><span class="sxs-lookup"><span data-stu-id="50602-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="50602-252">Bu nedenle GC, [büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap) (LOH) adı verilen _büyük_ nesneler için özel bir bellek bölgesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="50602-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="50602-253">85.000 bayttan (yaklaşık 83 KB) büyük nesneler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="50602-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="50602-254">LOH'a yerleştirildi.</span><span class="sxs-lookup"><span data-stu-id="50602-254">Placed on the LOH.</span></span>
* <span data-ttu-id="50602-255">Sıkıştırılmış değil.</span><span class="sxs-lookup"><span data-stu-id="50602-255">Not compacted.</span></span>
* <span data-ttu-id="50602-256">Nesil 2 GC'ler sırasında toplanır.</span><span class="sxs-lookup"><span data-stu-id="50602-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="50602-257">LOH dolduğunda, GC bir nesil 2 koleksiyonu tetikler.</span><span class="sxs-lookup"><span data-stu-id="50602-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="50602-258">Nesil 2 koleksiyonları:</span><span class="sxs-lookup"><span data-stu-id="50602-258">Generation 2 collections:</span></span>

* <span data-ttu-id="50602-259">Doğal olarak yavaşlar.</span><span class="sxs-lookup"><span data-stu-id="50602-259">Are inherently slow.</span></span>
* <span data-ttu-id="50602-260">Ayrıca, diğer tüm nesillerde bir koleksiyonu tetikleme maliyetine de neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="50602-261">Aşağıdaki kod hemen LOH kompakt:</span><span class="sxs-lookup"><span data-stu-id="50602-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="50602-262">LOH sıkıştırma hakkında bilgi için bkz. <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode></span><span class="sxs-lookup"><span data-stu-id="50602-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="50602-263">.NET Core 3.0 ve sonrası kullanan kaplarda LOH otomatik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="50602-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="50602-264">Bu davranışı gösteren aşağıdaki API:</span><span class="sxs-lookup"><span data-stu-id="50602-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="50602-265">Aşağıdaki grafik, maksimum yük altında `/api/loh/84975` bitiş noktasını çağırmanın bellek profilini gösterir:</span><span class="sxs-lookup"><span data-stu-id="50602-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![önceki grafik](memory/_static/loh1.png)

<span data-ttu-id="50602-267">Aşağıdaki grafik, `/api/loh/84976` bitiş noktasını çağırmanın bellek profilini gösterir ve yalnızca bir *bayt daha*ayırır:</span><span class="sxs-lookup"><span data-stu-id="50602-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![önceki grafik](memory/_static/loh2.png)

<span data-ttu-id="50602-269">Not: `byte[]` Yapının tepede baytları vardır.</span><span class="sxs-lookup"><span data-stu-id="50602-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="50602-270">Bu yüzden 84.976 bayt 85.000 limitini tetikler.</span><span class="sxs-lookup"><span data-stu-id="50602-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="50602-271">Önceki iki grafiğin karşılaştırılması:</span><span class="sxs-lookup"><span data-stu-id="50602-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="50602-272">Çalışma kümesi her iki senaryo için de benzer, yaklaşık 450 MB.</span><span class="sxs-lookup"><span data-stu-id="50602-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="50602-273">Altında LOH istekleri (84.975 bayt) çoğunlukla nesil 0 koleksiyonları gösterir.</span><span class="sxs-lookup"><span data-stu-id="50602-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="50602-274">Over LOH istekleri sürekli nesil 2 koleksiyonları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="50602-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="50602-275">Generation 2 koleksiyonları pahalıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="50602-276">Daha fazla CPU gereklidir ve iş çıktısı neredeyse %50 düşer.</span><span class="sxs-lookup"><span data-stu-id="50602-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="50602-277">Geçici büyük nesneler özellikle sorunludur, çünkü gen2 GC'lere neden olurlar.</span><span class="sxs-lookup"><span data-stu-id="50602-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="50602-278">Maksimum performans için, büyük nesne kullanımı en aza indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="50602-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="50602-279">Mümkünse, büyük nesneleri bölün.</span><span class="sxs-lookup"><span data-stu-id="50602-279">If possible, split up large objects.</span></span> <span data-ttu-id="50602-280">Örneğin, [Core'ASP.NET'daki Yanıt Önbelleğe Alma](xref:performance/caching/response) ara yazılımı önbellek girişlerini 85.000 bayttan daha az bloklara böler.</span><span class="sxs-lookup"><span data-stu-id="50602-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="50602-281">Aşağıdaki bağlantılar, nesneleri LOH sınırının altında tutmak için ASP.NET Core yaklaşımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="50602-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="50602-282">YanıtCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="50602-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="50602-283">YanıtCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="50602-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="50602-284">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="50602-284">For more information, see:</span></span>

* [<span data-ttu-id="50602-285">Büyük Nesne Yığını Ortaya Çıkarıldı</span><span class="sxs-lookup"><span data-stu-id="50602-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="50602-286">Büyük nesne yığını</span><span class="sxs-lookup"><span data-stu-id="50602-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="50602-287">httpİsteC</span><span class="sxs-lookup"><span data-stu-id="50602-287">HttpClient</span></span>

<span data-ttu-id="50602-288">Yanlış kullanmak <xref:System.Net.Http.HttpClient> kaynak sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="50602-289">Veritabanı bağlantıları, soketler, dosya tutamaçları vb. gibi sistem kaynakları:</span><span class="sxs-lookup"><span data-stu-id="50602-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="50602-290">Hafızadan daha az.</span><span class="sxs-lookup"><span data-stu-id="50602-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="50602-291">Sızdırıldığında bellekten daha sorunludur.</span><span class="sxs-lookup"><span data-stu-id="50602-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="50602-292">Deneyimli .NET geliştiricileri <xref:System.IDisposable.Dispose*> uygulayan <xref:System.IDisposable>nesneleri aramayı bilir.</span><span class="sxs-lookup"><span data-stu-id="50602-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="50602-293">Genellikle zamansız bellek veya `IDisposable` sızdırılan sistem kaynakları yla sonuçlanan nesneleri atmaz.</span><span class="sxs-lookup"><span data-stu-id="50602-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="50602-294">`HttpClient``IDisposable`uygular, ancak her çağrıüzerine **atılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="50602-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="50602-295">Bunun `HttpClient` yerine, yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="50602-296">Aşağıdaki bitiş noktası, her istekte `HttpClient` yeni bir örnek oluşturur ve bertaraf eder:</span><span class="sxs-lookup"><span data-stu-id="50602-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="50602-297">Yük altında, aşağıdaki hata iletileri günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="50602-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="50602-298">`HttpClient` Örnekler elden çıkarılmış olsa bile, gerçek ağ bağlantısının işletim sistemi tarafından serbest bırakılması biraz zaman alır.</span><span class="sxs-lookup"><span data-stu-id="50602-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="50602-299">Sürekli olarak yeni bağlantılar oluşturarak, _bağlantı noktalarının tükenmesi_ oluşur.</span><span class="sxs-lookup"><span data-stu-id="50602-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="50602-300">Her istemci bağlantısı kendi istemci bağlantı noktasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="50602-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="50602-301">Bağlantı noktası yorgunluğunu önlemenin bir yolu `HttpClient` da aynı örneği yeniden kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="50602-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="50602-302">Örnek, `HttpClient` uygulama durduğunda serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="50602-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="50602-303">Bu örnek, her tek kullanımlık kaynağın her kullanımdan sonra elden çıkarılmaması gerektiğini göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="50602-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="50602-304">Bir `HttpClient` örneğin kullanım ömrünü daha iyi bir şekilde işlemek için aşağıdakilere bakın:</span><span class="sxs-lookup"><span data-stu-id="50602-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="50602-305">Httpİste ve yaşam boyu yönetim</span><span class="sxs-lookup"><span data-stu-id="50602-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="50602-306">HTTPClient fabrika günlüğü</span><span class="sxs-lookup"><span data-stu-id="50602-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="50602-307">Nesne havuzu</span><span class="sxs-lookup"><span data-stu-id="50602-307">Object pooling</span></span>

<span data-ttu-id="50602-308">Önceki örnek, `HttpClient` örneğin nasıl statik yapılabilir ve tüm istekler tarafından yeniden gösterilebilir gösterdi.</span><span class="sxs-lookup"><span data-stu-id="50602-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="50602-309">Yeniden kullanım, kaynakların tükenmesini önler.</span><span class="sxs-lookup"><span data-stu-id="50602-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="50602-310">Nesne havuzu:</span><span class="sxs-lookup"><span data-stu-id="50602-310">Object pooling:</span></span>

* <span data-ttu-id="50602-311">Yeniden kullanım deseni kullanır.</span><span class="sxs-lookup"><span data-stu-id="50602-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="50602-312">Oluşturulması pahalı nesneler için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="50602-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="50602-313">Havuz, iş parçacıkları arasında rezerve edilebilen ve serbest bırakılabilen önceden başlatılanmış nesneler topluluğudur.</span><span class="sxs-lookup"><span data-stu-id="50602-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="50602-314">Havuzlar, sınırlar, önceden tanımlanmış boyutlar veya büyüme hızı gibi ayırma kuralları tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="50602-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="50602-315">NuGet paketi [Microsoft.Extensions.ObjectPool,](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) bu tür havuzları yönetmeye yardımcı olan sınıflar içerir.</span><span class="sxs-lookup"><span data-stu-id="50602-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="50602-316">Aşağıdaki API bitiş noktası, her `byte` istekte rasgele sayılarla dolu bir arabellek anında gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="50602-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="50602-317">Aşağıdaki grafik ekranı önceki API'yi orta düzeyde yükile çağırarak gösterir:</span><span class="sxs-lookup"><span data-stu-id="50602-317">The following chart display calling the preceding API with moderate load:</span></span>

![önceki grafik](memory/_static/array.png)

<span data-ttu-id="50602-319">Önceki grafikte, nesil 0 koleksiyonları saniyede yaklaşık bir kez gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="50602-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="50602-320">Önceki kod [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1)kullanarak `byte` arabellek havuzlama tarafından optimize edilebilir.</span><span class="sxs-lookup"><span data-stu-id="50602-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="50602-321">Statik bir örnek istekler arasında yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50602-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="50602-322">Bu yaklaşımda farklı olan, birleştirilmiş bir nesnenin API'den döndürülür olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="50602-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="50602-323">Bu şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="50602-323">That means:</span></span>

* <span data-ttu-id="50602-324">Yöntemden döner dönmez nesne sizin denetiminizde değildir.</span><span class="sxs-lookup"><span data-stu-id="50602-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="50602-325">Nesneyi serbest bırakamazsın.</span><span class="sxs-lookup"><span data-stu-id="50602-325">You can't release the object.</span></span>

<span data-ttu-id="50602-326">Nesnenin bertarafını ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="50602-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="50602-327">Tek kullanımlık bir nesnede birleştirilmiş diziyi kapsülle.</span><span class="sxs-lookup"><span data-stu-id="50602-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="50602-328">Havuza toplanan nesneyi [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="50602-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="50602-329">`RegisterForDispose`yalnızca HTTP isteği `Dispose`tamamlandığında serbest bırakılması için hedef nesneyi arama yı önemseyecektir.</span><span class="sxs-lookup"><span data-stu-id="50602-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="50602-330">Havuza ait olmayan sürümle aynı yükün uygulanması aşağıdaki grafikte sonuçlanır:</span><span class="sxs-lookup"><span data-stu-id="50602-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![önceki grafik](memory/_static/pooledarray.png)

<span data-ttu-id="50602-332">Ana fark bayt ayrılır ve sonuç olarak çok daha az nesil 0 koleksiyonları.</span><span class="sxs-lookup"><span data-stu-id="50602-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50602-333">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50602-333">Additional resources</span></span>

* [<span data-ttu-id="50602-334">Çöp Toplama</span><span class="sxs-lookup"><span data-stu-id="50602-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="50602-335">Eşzamanlı Görselleştirici ile farklı GC modlarını anlama</span><span class="sxs-lookup"><span data-stu-id="50602-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="50602-336">Büyük Nesne Yığını Ortaya Çıkarıldı</span><span class="sxs-lookup"><span data-stu-id="50602-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="50602-337">Büyük nesne yığını</span><span class="sxs-lookup"><span data-stu-id="50602-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
