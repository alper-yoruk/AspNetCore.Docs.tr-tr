---
title: ASP.NET Core 'de günlüğe kaydetme ve tanılama SignalR
author: bradygaster
description: ASP.NET Core uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
no-loc:
- appsettings.json
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
uid: signalr/diagnostics
ms.openlocfilehash: 23ebd61d9931f9cd83afbdcc5a718e42cc565317
ms.sourcegitcommit: b23fed8c1a1d2aec2f9b5e09041442ecfafedd56
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797346"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="be4b4-103">ASP.NET Core 'de günlüğe kaydetme ve tanılama SignalR</span><span class="sxs-lookup"><span data-stu-id="be4b4-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="be4b4-104">, [Andrew Stanton-nurte](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="be4b4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="be4b4-105">Bu makalede, SignalR sorunları gidermeye yardımcı olmak için ASP.NET Core uygulamanızdan tanılama toplamaya yönelik rehberlik sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="be4b4-106">Sunucu tarafında günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="be4b4-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="be4b4-107">Sunucu tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="be4b4-108">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="be4b4-109">SignalRASP.NET Core bir parçası olduğundan, ASP.NET Core günlük sistemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="be4b4-110">Varsayılan yapılandırmada SignalR çok az bilgi günlüğe kaydedilir, ancak bu yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="be4b4-111">ASP.NET Core günlüğü yapılandırma hakkında ayrıntılar için [ASP.NET Core günlüğe kaydetme](xref:fundamentals/logging/index#configuration) hakkındaki belgelere bakın.</span><span class="sxs-lookup"><span data-stu-id="be4b4-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="be4b4-112">SignalR İki Günlükçü kategorisi kullanır:</span><span class="sxs-lookup"><span data-stu-id="be4b4-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="be4b4-113">`Microsoft.AspNetCore.SignalR`: Merkez protokolleriyle ilgili Günlükler için, hub 'Ları etkinleştirme, yöntemleri çağırma ve hub ile ilgili diğer etkinlikler için.</span><span class="sxs-lookup"><span data-stu-id="be4b4-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="be4b4-114">`Microsoft.AspNetCore.Http.Connections`: WebSockets, uzun yoklama, Server-Sent olayları ve alt düzey altyapı gibi aktarımlarıyla ilgili Günlükler için SignalR .</span><span class="sxs-lookup"><span data-stu-id="be4b4-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="be4b4-115">Ayrıntılı günlükleri ' den etkinleştirmek için SignalR , `Debug` *appsettings.json* aşağıdaki öğeleri `LogLevel` içindeki alt bölümüne ekleyerek önceki ön eklerin her ikisini de dosyanızdaki düzeye yapılandırın `Logging` :</span><span class="sxs-lookup"><span data-stu-id="be4b4-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="be4b4-116">Ayrıca, bu kodu yönteminizin içinde de yapılandırabilirsiniz `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="be4b4-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="be4b4-117">JSON tabanlı yapılandırma kullanmıyorsanız, yapılandırma sisteminizde aşağıdaki yapılandırma değerlerini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="be4b4-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="be4b4-118">İç içe yapılandırma değerlerinin nasıl belirleneceğini belirlemek için yapılandırma sisteminizin belgelerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="be4b4-119">Örneğin, ortam değişkenleri kullanılırken, `_` yerine iki karakter kullanılır `:` (örneğin, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="be4b4-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="be4b4-120">`Debug`Uygulamanız için daha ayrıntılı tanılama toplanırken düzeyin kullanılmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="be4b4-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="be4b4-121">`Trace`Düzey, çok düşük düzey Tanılamalar üretir ve uygulamanızdaki sorunları tanılamak için nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="be4b4-122">Sunucu tarafı günlüklerine erişin</span><span class="sxs-lookup"><span data-stu-id="be4b4-122">Access server-side logs</span></span>

<span data-ttu-id="be4b4-123">Sunucu tarafı günlüklerine erişme, çalıştırdığınız ortama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="be4b4-124">IIS dışında bir konsol uygulaması olarak</span><span class="sxs-lookup"><span data-stu-id="be4b4-124">As a console app outside IIS</span></span>

<span data-ttu-id="be4b4-125">Konsol uygulamasında çalıştırıyorsanız, [konsol günlükçüsü](xref:fundamentals/logging/index#console) varsayılan olarak etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="be4b4-126">SignalR Günlükler konsolunda görünür.</span><span class="sxs-lookup"><span data-stu-id="be4b4-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="be4b4-127">Visual Studio 'dan IIS Express içinde</span><span class="sxs-lookup"><span data-stu-id="be4b4-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="be4b4-128">Visual Studio **çıktı** penceresinde günlük çıktısını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="be4b4-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="be4b4-129">**ASP.NET Core Web sunucusu** açılır seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="be4b4-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="be4b4-130">Azure App Service</span></span>

<span data-ttu-id="be4b4-131">Azure App Service portalının **tanılama günlükleri** bölümünde **uygulama günlüğü (dosya sistemi)** seçeneğini etkinleştirin ve **düzeyini** olarak yapılandırın `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="be4b4-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="be4b4-132">Günlükler **günlük akış** hizmetinden ve App Service dosya sistemindeki günlüklerde kullanılabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="be4b4-133">Daha fazla bilgi için bkz. [Azure günlük akışı](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="be4b4-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="be4b4-134">Diğer ortamlar</span><span class="sxs-lookup"><span data-stu-id="be4b4-134">Other environments</span></span>

<span data-ttu-id="be4b4-135">Uygulama başka bir ortama (örneğin, Docker, Kubernetes veya Windows hizmeti) dağıtılırsa, <xref:fundamentals/logging/index> ortama uygun günlük sağlayıcılarının nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz..</span><span class="sxs-lookup"><span data-stu-id="be4b4-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="be4b4-136">JavaScript istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="be4b4-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="be4b4-137">İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="be4b4-138">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="be4b4-139">JavaScript istemcisini kullanırken, oturum açma seçeneklerini kullanarak şu `configureLogging` yöntemi kullanabilirsiniz `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="be4b4-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="be4b4-140">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="be4b4-141">Aşağıdaki tabloda JavaScript istemcisi için kullanılabilir olan günlük düzeyleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="be4b4-142">Günlük düzeyinin bu değerlerden birine ayarlanması, bu düzeyde ve tabloda üzerindeki tüm düzeylerde günlüğe kaydetmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="be4b4-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="be4b4-143">Level</span><span class="sxs-lookup"><span data-stu-id="be4b4-143">Level</span></span> | <span data-ttu-id="be4b4-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="be4b4-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="be4b4-145">Hiçbir ileti günlüğe kaydedilmez.</span><span class="sxs-lookup"><span data-stu-id="be4b4-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="be4b4-146">Uygulamanın tamamında bir hata olduğunu gösteren mesajlar.</span><span class="sxs-lookup"><span data-stu-id="be4b4-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="be4b4-147">Geçerli işlemdeki bir hatayı gösteren mesajlar.</span><span class="sxs-lookup"><span data-stu-id="be4b4-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="be4b4-148">Önemli olmayan bir sorunu belirten mesajlar.</span><span class="sxs-lookup"><span data-stu-id="be4b4-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="be4b4-149">Bilgi iletileri.</span><span class="sxs-lookup"><span data-stu-id="be4b4-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="be4b4-150">Tanılama iletileri hata ayıklama için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="be4b4-151">Belirli sorunları tanılamak için tasarlanan çok ayrıntılı tanılama iletileri.</span><span class="sxs-lookup"><span data-stu-id="be4b4-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="be4b4-152">Ayrıntı düzeyini yapılandırdıktan sonra, Günlükler tarayıcı konsoluna yazılır (veya bir NodeJS uygulamasında standart çıkış).</span><span class="sxs-lookup"><span data-stu-id="be4b4-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="be4b4-153">Günlükleri özel bir günlüğe kaydetme sistemine göndermek istiyorsanız, arabirimini uygulayan bir JavaScript nesnesi sağlayabilirsiniz `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="be4b4-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="be4b4-154">Uygulanması gereken tek yöntem `log` , olay düzeyini ve olayla ilişkili iletiyi alır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="be4b4-155">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="be4b4-155">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-typescript[](diagnostics/3.x/custom-logger.ts?highlight=3-7,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-typescript[](diagnostics/2.x/custom-logger.ts?highlight=3-7,13)]

::: moniker-end

## <a name="net-client-logging"></a><span data-ttu-id="be4b4-156">.NET istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="be4b4-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="be4b4-157">İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="be4b4-158">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="be4b4-159">.NET istemcisinden günlükleri almak için, `ConfigureLogging` üzerinde yöntemini kullanabilirsiniz `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="be4b4-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="be4b4-160">Bu, `ConfigureLogging` ve üzerindeki yöntemiyle aynı şekilde çalışmaktadır `WebHostBuilder` `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="be4b4-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="be4b4-161">ASP.NET Core ' de kullandığınız günlük sağlayıcılarını yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="be4b4-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="be4b4-162">Ancak, bireysel günlük sağlayıcıları için NuGet paketlerini el ile yükleyip etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="be4b4-163">Bir uygulamaya .NET istemci günlüğü eklemek için Blazor WebAssembly , bkz <xref:blazor/fundamentals/logging#signalr-net-client-logging> ..</span><span class="sxs-lookup"><span data-stu-id="be4b4-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="be4b4-164">Konsol günlüğü</span><span class="sxs-lookup"><span data-stu-id="be4b4-164">Console logging</span></span>

<span data-ttu-id="be4b4-165">Konsol günlüğünü etkinleştirmek için [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="be4b4-166">Ardından, `AddConsole` konsol günlükçüsü 'yi yapılandırmak için yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="be4b4-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="be4b4-167">Çıkış penceresi günlüğüne hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="be4b4-167">Debug output window logging</span></span>

<span data-ttu-id="be4b4-168">Günlükleri, Visual Studio 'daki **Çıkış** penceresine gitmek için de yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="be4b4-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="be4b4-169">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) paketini yükleyip `AddDebug` yöntemi kullanın:</span><span class="sxs-lookup"><span data-stu-id="be4b4-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="be4b4-170">Diğer günlüğe kaydetme sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="be4b4-170">Other logging providers</span></span>

<span data-ttu-id="be4b4-171">SignalR Serilog, seq, NLog gibi diğer günlük sağlayıcılarını veya ile tümleştirilen diğer bir günlük sistemini destekler `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="be4b4-171">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="be4b4-172">Günlük sisteminiz bir sağlıyorsa `ILoggerProvider` , şunu kullanarak kaydedebilirsiniz `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="be4b4-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="be4b4-173">Denetim ayrıntı düzeyi</span><span class="sxs-lookup"><span data-stu-id="be4b4-173">Control verbosity</span></span>

<span data-ttu-id="be4b4-174">Uygulamanızdaki diğer yerlerden oturum açıyorsanız, varsayılan düzeyin olarak değiştirilmesi `Debug` çok ayrıntılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="be4b4-175">Günlükler için günlüğe kaydetme düzeyini yapılandırmak üzere bir filtre kullanabilirsiniz SignalR .</span><span class="sxs-lookup"><span data-stu-id="be4b4-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="be4b4-176">Bu, sunucuda olduğu şekilde kodda yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="be4b4-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="be4b4-177">Ağ izlemeleri</span><span class="sxs-lookup"><span data-stu-id="be4b4-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="be4b4-178">Bir ağ izlemesi, uygulamanız tarafından gönderilen her iletinin tam içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="be4b4-179">Ham ağ izlemelerini, üretim uygulamalarından GitHub gibi genel forumlara **hiçbir** şekilde yayımlayamazsınız.</span><span class="sxs-lookup"><span data-stu-id="be4b4-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="be4b4-180">Bir sorunla karşılaşırsanız, ağ izleme bazen yararlı olabilecek çok sayıda bilgi sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="be4b4-181">Sorun izleyicimizde bir sorun oluşturacaksanız bu özellikle yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="be4b4-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="be4b4-182">Fiddler ile ağ izleme toplama (tercih edilen seçenek)</span><span class="sxs-lookup"><span data-stu-id="be4b4-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="be4b4-183">Bu yöntem tüm uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-183">This method works for all apps.</span></span>

<span data-ttu-id="be4b4-184">Fiddler, HTTP izlemelerinin toplanması için çok güçlü bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="be4b4-185">[Telerik.com/Fiddler](https://www.telerik.com/fiddler)adresinden yükleyip uygulamayı çalıştırın ve sorunu yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="be4b4-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="be4b4-186">Fiddler Windows için kullanılabilir ve macOS ve Linux için beta sürümleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="be4b4-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="be4b4-187">HTTPS kullanarak bağlanıyorsanız, Fiddler 'ın HTTPS trafiğinin şifresini çözebilmesini sağlamaya yönelik bazı ek adımlar vardır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="be4b4-188">Daha ayrıntılı bilgi için bkz. [Fiddler belgeleri](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="be4b4-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="be4b4-189">İzlemeyi topladıktan sonra, **Dosya**  >    >  **tüm oturumları** menü çubuğundan Kaydet ' i seçerek izlemeyi dışarı aktarabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="be4b4-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler 'tan tüm oturumlar dışarı aktarılıyor](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="be4b4-191">Tcpdump ile bir ağ izlemesi toplayın (yalnızca macOS ve Linux)</span><span class="sxs-lookup"><span data-stu-id="be4b4-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="be4b4-192">Bu yöntem tüm uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-192">This method works for all apps.</span></span>

<span data-ttu-id="be4b4-193">Komut kabuğundan aşağıdaki komutu çalıştırarak, tcpdump kullanarak ham TCP izlemeleri toplayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="be4b4-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="be4b4-194">`root`Bir izin hatası alırsanız, komutuna sahip olmanız veya komuta önek uygulamanız gerekebilir `sudo` :</span><span class="sxs-lookup"><span data-stu-id="be4b4-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="be4b4-195">`[interface]`Yakalamak istediğiniz ağ arabirimiyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="be4b4-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="be4b4-196">Genellikle bu, `/dev/eth0` (Standart Ethernet arabiriminiz için) veya `/dev/lo0` (localhost trafiği için) gibi bir şeydir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="be4b4-197">Daha fazla bilgi için bkz `tcpdump` . ana bilgisayar sisteminizdeki adam sayfası.</span><span class="sxs-lookup"><span data-stu-id="be4b4-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="be4b4-198">Tarayıcıda bir ağ izlemesi toplayın</span><span class="sxs-lookup"><span data-stu-id="be4b4-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="be4b4-199">Bu yöntem yalnızca tarayıcı tabanlı uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="be4b4-200">Çoğu tarayıcı Geliştirici Araçları, tarayıcı ve sunucu arasında ağ etkinliğini yakalamanızı sağlayan bir "ağ" sekmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="be4b4-201">Ancak, bu izlemeler WebSocket ve Server-Sent olay iletileri içermez.</span><span class="sxs-lookup"><span data-stu-id="be4b4-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="be4b4-202">Bu taşımaları kullanıyorsanız, Fiddler veya TcpDump (aşağıda açıklanmıştır) gibi bir araç kullanmak daha iyi bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="be4b4-203">Microsoft Edge ve Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="be4b4-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="be4b4-204">(Yönergeler hem Edge hem de Internet Explorer için aynıdır)</span><span class="sxs-lookup"><span data-stu-id="be4b4-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="be4b4-205">Geliştirici araçlarını açmak için F12 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="be4b4-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="be4b4-206">Ağ sekmesine tıklayın</span><span class="sxs-lookup"><span data-stu-id="be4b4-206">Click the Network Tab</span></span>
3. <span data-ttu-id="be4b4-207">Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="be4b4-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="be4b4-208">İzlemeyi "HAR" dosyası olarak dışarı aktarmak için araç çubuğundaki Kaydet simgesine tıklayın:</span><span class="sxs-lookup"><span data-stu-id="be4b4-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge geliştirme araçları Ağ sekmesinde Kaydet simgesi](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="be4b4-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="be4b4-210">Google Chrome</span></span>

1. <span data-ttu-id="be4b4-211">Geliştirici araçlarını açmak için F12 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="be4b4-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="be4b4-212">Ağ sekmesine tıklayın</span><span class="sxs-lookup"><span data-stu-id="be4b4-212">Click the Network Tab</span></span>
3. <span data-ttu-id="be4b4-213">Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="be4b4-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="be4b4-214">İstek listesinde herhangi bir yere sağ tıklayın ve "İçerikle HAR olarak Kaydet" i seçin:</span><span class="sxs-lookup"><span data-stu-id="be4b4-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome geliştirme araçları Ağ sekmesinde "Içerik ile HAR olarak Kaydet" seçeneği](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="be4b4-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="be4b4-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="be4b4-217">Geliştirici araçlarını açmak için F12 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="be4b4-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="be4b4-218">Ağ sekmesine tıklayın</span><span class="sxs-lookup"><span data-stu-id="be4b4-218">Click the Network Tab</span></span>
3. <span data-ttu-id="be4b4-219">Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="be4b4-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="be4b4-220">İstek listesinde herhangi bir yere sağ tıklayın ve "tümünü HAR olarak Kaydet" i seçin</span><span class="sxs-lookup"><span data-stu-id="be4b4-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox geliştirme araçları Ağ sekmesinde "tümünü HAR olarak Kaydet" seçeneği](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="be4b4-222">GitHub sorunlarına tanılama dosyaları iliştirme</span><span class="sxs-lookup"><span data-stu-id="be4b4-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="be4b4-223">Tanılama dosyalarını, bir uzantıya sahip olacak şekilde yeniden adlandırarak `.txt` ve sonra sorunu üzerine sürükleyip bırakarak GitHub sorunlarına iliştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="be4b4-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="be4b4-224">Lütfen günlük dosyalarının veya ağ izlemelerinin içeriğini bir GitHub sorununa yapıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="be4b4-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="be4b4-225">Bu Günlükler ve izlemeler oldukça büyük olabilir ve GitHub genellikle bunları keser.</span><span class="sxs-lookup"><span data-stu-id="be4b4-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Günlük dosyalarını bir GitHub sorununa sürükleme](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="be4b4-227">Ölçümler</span><span class="sxs-lookup"><span data-stu-id="be4b4-227">Metrics</span></span>

<span data-ttu-id="be4b4-228">Ölçümler, zaman aralıklarıyla veri ölçümlerinin bir gösterimidir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="be4b4-229">Örneğin, saniye başına istek.</span><span class="sxs-lookup"><span data-stu-id="be4b4-229">For example, requests per second.</span></span> <span data-ttu-id="be4b4-230">Ölçüm verileri, yüksek düzeyde bir uygulamanın durumunun gözlemde yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="be4b4-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="be4b4-231">.NET gRPC ölçümleri kullanılarak dağıtılır <xref:System.Diagnostics.Tracing.EventCounter> .</span><span class="sxs-lookup"><span data-stu-id="be4b4-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="be4b4-232">SignalR Sunucu ölçümleri</span><span class="sxs-lookup"><span data-stu-id="be4b4-232">SignalR server metrics</span></span>

<span data-ttu-id="be4b4-233">SignalR Sunucu ölçümleri <xref:Microsoft.AspNetCore.Http.Connections> olay kaynağında raporlanır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-233">SignalR server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="be4b4-234">Ad</span><span class="sxs-lookup"><span data-stu-id="be4b4-234">Name</span></span>                    | <span data-ttu-id="be4b4-235">Açıklama</span><span class="sxs-lookup"><span data-stu-id="be4b4-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="be4b4-236">Toplam bağlantı sayısı</span><span class="sxs-lookup"><span data-stu-id="be4b4-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="be4b4-237">Durdurulan toplam bağlantı sayısı</span><span class="sxs-lookup"><span data-stu-id="be4b4-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="be4b4-238">Zaman aşımına uğrayan toplam bağlantı sayısı</span><span class="sxs-lookup"><span data-stu-id="be4b4-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="be4b4-239">Geçerli bağlantılar</span><span class="sxs-lookup"><span data-stu-id="be4b4-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="be4b4-240">Ortalama bağlantı süresi</span><span class="sxs-lookup"><span data-stu-id="be4b4-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="be4b4-241">Ölçümleri gözlemleyin</span><span class="sxs-lookup"><span data-stu-id="be4b4-241">Observe metrics</span></span>

<span data-ttu-id="be4b4-242">[DotNet sayaçları](/dotnet/core/diagnostics/dotnet-counters) , geçici sistem durumu izleme ve ilk düzey performans araştırması için bir performans izleme aracıdır.</span><span class="sxs-lookup"><span data-stu-id="be4b4-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="be4b4-243">Sağlayıcı adı olarak bir .NET uygulamasını izleyin `Microsoft.AspNetCore.Http.Connections` .</span><span class="sxs-lookup"><span data-stu-id="be4b4-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="be4b4-244">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="be4b4-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="be4b4-245">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="be4b4-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
