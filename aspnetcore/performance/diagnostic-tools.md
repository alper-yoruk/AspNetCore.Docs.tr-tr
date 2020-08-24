---
title: Performans Tanılama araçları
author: mjrousos
description: ASP.NET Core uygulamalarında performans sorunlarını tanılamaya yönelik faydalı araçlar.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 5f3daaf132b903898e756160a459d4df5f421c11
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746552"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="51df2-103">Performans Tanılama Araçları</span><span class="sxs-lookup"><span data-stu-id="51df2-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="51df2-104">, [Mike Rousos](https://github.com/mjrousos) tarafından</span><span class="sxs-lookup"><span data-stu-id="51df2-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="51df2-105">Bu makalede ASP.NET Core performans sorunlarını tanılamaya yönelik araçlar listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="51df2-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="51df2-106">Visual Studio Tanılama Araçları</span><span class="sxs-lookup"><span data-stu-id="51df2-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="51df2-107">Visual Studio 'da yerleşik olarak bulunan [profil oluşturma ve tanılama araçları](/visualstudio/profiling) , performans sorunlarını araştırmaya başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="51df2-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="51df2-108">Bu araçlar, Visual Studio geliştirme ortamından güçlü ve kullanımı kolay bir yöntemdir.</span><span class="sxs-lookup"><span data-stu-id="51df2-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="51df2-109">Araç, ASP.NET Core uygulamalardaki CPU kullanımı, bellek kullanımı ve performans olaylarının analizine izin verir.</span><span class="sxs-lookup"><span data-stu-id="51df2-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="51df2-110">Yerleşik olarak, geliştirme sırasında profil oluşturma kolaylaşır.</span><span class="sxs-lookup"><span data-stu-id="51df2-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="51df2-111">[Visual Studio belgelerinde](/visualstudio/profiling/profiling-overview)daha fazla bilgi bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51df2-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="51df2-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="51df2-112">Application Insights</span></span>

<span data-ttu-id="51df2-113">[Application Insights](/azure/application-insights/app-insights-overview) , uygulamanız için ayrıntılı performans verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="51df2-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="51df2-114">Application Insights, yanıt oranları, hata oranları, bağımlılık yanıt süreleri ve daha fazlası için otomatik olarak veri toplar.</span><span class="sxs-lookup"><span data-stu-id="51df2-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="51df2-115">Application Insights, uygulamanıza özel olayları ve ölçümleri günlüğe kaydetmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="51df2-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="51df2-116">Azure Application Insights, izlenen uygulamalar hakkında öngörü sağlamak için birden çok yol sağlar:</span><span class="sxs-lookup"><span data-stu-id="51df2-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="51df2-117">[Uygulama eşlemesi](/azure/application-insights/app-insights-app-map) – Dağıtılmış uygulamaların tüm bileşenlerinde performans sorunlarını ve hata durumunda etkin noktaları belirlemesine yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="51df2-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="51df2-118">[Azure Ölçüm Gezgini](/azure/azure-monitor/platform/metrics-getting-started) , grafikleri çizdirme, eğilimleri görsel olarak ilişkilendirme ve ölçüm değerlerinde ani artışları ve DIP 'leri araştırmanıza olanak tanıyan bir Microsoft Azure Portal bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="51df2-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="51df2-119">[Application Insights portalındaki performans dikey](/azure/application-insights/app-insights-tutorial-performance)penceresi:</span><span class="sxs-lookup"><span data-stu-id="51df2-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="51df2-120">İzlenen uygulamadaki farklı işlemlere yönelik performans ayrıntılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="51df2-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="51df2-121">Uzun bir süreye katkıda bulunan tüm parçaları/bağımlılıkları denetlemek için tek bir işleme detaya gitmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="51df2-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="51df2-122">Profil Oluşturucu, isteğe bağlı performans izlemeleri toplamak için Buradan çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="51df2-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="51df2-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) , .NET uygulamalarının düzenli ve isteğe bağlı olarak profil oluşturulmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="51df2-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="51df2-124">Azure portal, çağrı yığınları ve etkin yollarla yakalanan performans izlerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="51df2-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="51df2-125">İzleme dosyaları PerfView kullanılarak daha derin analizler için de indirilebilir.</span><span class="sxs-lookup"><span data-stu-id="51df2-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="51df2-126">Application Insights, çeşitli ortamlarda kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51df2-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="51df2-127">Azure 'da çalışmak için iyileştirildi.</span><span class="sxs-lookup"><span data-stu-id="51df2-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="51df2-128">Üretim, geliştirme ve hazırlama için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="51df2-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="51df2-129">[Visual Studio](/azure/application-insights/app-insights-visual-studio) 'dan veya diğer barındırma ortamlarında yerel olarak çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="51df2-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="51df2-130">Daha fazla bilgi için bkz. [ASP.NET Core Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="51df2-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="51df2-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="51df2-131">PerfView</span></span>

<span data-ttu-id="51df2-132">[PerfView](https://github.com/Microsoft/perfview) , .NET ekibi tarafından .net performans sorunlarını tanılamak için özel olarak oluşturulan bir performans analizi aracıdır.</span><span class="sxs-lookup"><span data-stu-id="51df2-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="51df2-133">PerfView CPU kullanımı, bellek ve GC davranışı, performans olayları ve duvar saati saatinin analizine izin verir.</span><span class="sxs-lookup"><span data-stu-id="51df2-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="51df2-134">PerfView hakkında daha fazla bilgi edinmek ve [PerfView video öğreticileri](https://channel9.msdn.com/Series/PerfView-Tutorial) ile çalışmaya başlama ya da araç veya [GitHub üzerinde](https://github.com/Microsoft/perfview)bulunan kullanıcı kılavuzunu okuma hakkında daha fazla bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51df2-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="51df2-135">Windows performans araç seti</span><span class="sxs-lookup"><span data-stu-id="51df2-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="51df2-136">[Windows performans araç seti](/windows-hardware/test/wpt/) (WPT) iki bileşenden oluşur: Windows performans Kaydedicisi (WPR) ve Windows performans ÇÖZÜMLEYICISI (WPA).</span><span class="sxs-lookup"><span data-stu-id="51df2-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="51df2-137">Araçlar, Windows işletim sistemlerinin ve uygulamalarının ayrıntılı performans profillerini üretir.</span><span class="sxs-lookup"><span data-stu-id="51df2-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="51df2-138">WPT, verileri görselleştirmenin daha zengin yollarına sahiptir, ancak veri toplama işlemi PerfView 'dan daha az güçlüdür.</span><span class="sxs-lookup"><span data-stu-id="51df2-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="51df2-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="51df2-139">PerfCollect</span></span>

<span data-ttu-id="51df2-140">PerfView, .NET senaryoları için faydalı bir performans Analizi Aracı olsa da, yalnızca Windows üzerinde çalışır, bu sayede Linux ortamlarında çalışan ASP.NET Core uygulamalardan izleme toplamak için kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="51df2-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="51df2-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) , PerfView tarafından çözümlenebilecek Linux üzerinde izlemeler toplamak Için yerel Linux profil oluşturma araçları 'Nı ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) ve [lttng](https://lttng.org/)) kullanan bir bash betiğdir.</span><span class="sxs-lookup"><span data-stu-id="51df2-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="51df2-142">PerfCollect, PerfView 'in doğrudan kullanılabileceği Linux ortamlarında görüntülendiğinde yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="51df2-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="51df2-143">Bunun yerine, PerfCollect .NET Core uygulamalarından daha sonra PerfView kullanılarak bir Windows bilgisayarında çözümlenen izlemeler toplayabilir.</span><span class="sxs-lookup"><span data-stu-id="51df2-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="51df2-144">PerfCollect 'i yüklemek ve kullanmaya başlamak hakkında daha fazla bilgiyi [GitHub ' da](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51df2-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="51df2-145">Diğer üçüncü taraf performans araçları</span><span class="sxs-lookup"><span data-stu-id="51df2-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="51df2-146">Aşağıda, .NET Core uygulamalarının performans araştırmasına faydalı olan bazı üçüncü taraf performans araçları listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="51df2-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="51df2-147">Mini profil Oluşturucu</span><span class="sxs-lookup"><span data-stu-id="51df2-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="51df2-148">[JetBrains](https://www.jetbrains.com/) 'Den [Dottrace](https://www.jetbrains.com/profiler/) ve [dotmemory](https://www.jetbrains.com/dotmemory/)</span><span class="sxs-lookup"><span data-stu-id="51df2-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="51df2-149">Intel 'ten [sanal ayar](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)</span><span class="sxs-lookup"><span data-stu-id="51df2-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
