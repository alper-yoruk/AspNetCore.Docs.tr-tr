---
title: ASP.NET Core modülüyle günlük oluşturma ve yeniden yönlendirme
author: rick-anderson
description: Günlükleri ve tanılama bilgilerini yakalamak için IIS ve ASP.NET Core modülünü yapılandırın.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057446"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="d8ab7-103">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="d8ab7-103">Log creation and redirection</span></span>

<span data-ttu-id="d8ab7-104">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="d8ab7-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="d8ab7-105">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="d8ab7-106">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\{APP POOL NAME}` yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu yazma iznini sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="d8ab7-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="d8ab7-107">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="d8ab7-108">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="d8ab7-109">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="d8ab7-110">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="d8ab7-111">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d8ab7-112">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="d8ab7-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="d8ab7-113">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="d8ab7-114">Günlük dosyası adı, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ), yolun son kesimine `stdoutLogFile` (genellikle `stdout` ) alt çizgi ile ayrılmış olarak eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="d8ab7-115">`stdoutLogFile`Yol ile sonlanıyorsa `stdout` , 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı dosya adına sahiptir `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="d8ab7-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="d8ab7-116">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="d8ab7-117">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="d8ab7-118">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="d8ab7-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="d8ab7-119">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="d8ab7-120">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="d8ab7-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="d8ab7-121">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="d8ab7-122">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="d8ab7-123">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="d8ab7-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="d8ab7-124">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="d8ab7-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="d8ab7-125">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="d8ab7-126">`<handlerSettings>`Öğesini `<aspNetCore>` içindeki öğesine ekleyin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="d8ab7-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="d8ab7-127">İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="d8ab7-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="d8ab7-128">Yoldaki tüm klasörler ( `logs` Önceki örnekte), günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="d8ab7-129">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\{APP POOL NAME}` yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu yazma iznini sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="d8ab7-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="d8ab7-130">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="d8ab7-131">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="d8ab7-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="d8ab7-132">HATA</span><span class="sxs-lookup"><span data-stu-id="d8ab7-132">ERROR</span></span>
* <span data-ttu-id="d8ab7-133">UYARI</span><span class="sxs-lookup"><span data-stu-id="d8ab7-133">WARNING</span></span>
* <span data-ttu-id="d8ab7-134">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="d8ab7-134">INFO</span></span>
* <span data-ttu-id="d8ab7-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="d8ab7-135">TRACE</span></span>

<span data-ttu-id="d8ab7-136">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="d8ab7-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="d8ab7-137">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="d8ab7-137">CONSOLE</span></span>
* <span data-ttu-id="d8ab7-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="d8ab7-138">EVENTLOG</span></span>
* <span data-ttu-id="d8ab7-139">DOSYA</span><span class="sxs-lookup"><span data-stu-id="d8ab7-139">FILE</span></span>

<span data-ttu-id="d8ab7-140">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d8ab7-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="d8ab7-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="d8ab7-142">(Varsayılan: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="d8ab7-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="d8ab7-143">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="d8ab7-144">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="d8ab7-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="d8ab7-145">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-145">The size of the log isn't limited.</span></span> <span data-ttu-id="d8ab7-146">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="d8ab7-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="d8ab7-147">Dosyadaki öğenin bir örneği için [ile `web.config` ASP.NET Core modülün yapılandırmasına](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) bakın `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="d8ab7-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
