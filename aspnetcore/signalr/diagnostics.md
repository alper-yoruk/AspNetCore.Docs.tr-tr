---
<span data-ttu-id="e2a6f-101">Başlık: ' ASP.NET Core ' de günlüğe kaydetme ve tanılama SignalR : Açıklama: ' ASP.NET Core uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin SignalR . '</span><span class="sxs-lookup"><span data-stu-id="e2a6f-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e2a6f-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2a6f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-103">'Blazor'</span></span>
- <span data-ttu-id="e2a6f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-104">'Identity'</span></span>
- <span data-ttu-id="e2a6f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2a6f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-106">'Razor'</span></span>
- <span data-ttu-id="e2a6f-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="e2a6f-108">ASP.NET Core 'de günlüğe kaydetme ve tanılamaSignalR</span><span class="sxs-lookup"><span data-stu-id="e2a6f-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="e2a6f-109">, [Andrew Stanton-nurte](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e2a6f-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="e2a6f-110">Bu makalede, SignalR sorunları gidermeye yardımcı olmak için ASP.NET Core uygulamanızdan tanılama toplamaya yönelik rehberlik sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="e2a6f-111">Sunucu tarafında günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="e2a6f-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="e2a6f-112">Sunucu tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e2a6f-113">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e2a6f-114">SignalRASP.NET Core bir parçası olduğundan, ASP.NET Core günlük sistemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="e2a6f-115">Varsayılan yapılandırmada SignalR çok az bilgi günlüğe kaydedilir, ancak bu yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="e2a6f-116">ASP.NET Core günlüğü yapılandırma hakkında ayrıntılar için [ASP.NET Core günlüğe kaydetme](xref:fundamentals/logging/index#configuration) hakkındaki belgelere bakın.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="e2a6f-117">İki Günlükçü kategorisi kullanır:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-117"> uses two logger categories:</span></span>

* <span data-ttu-id="e2a6f-118">`Microsoft.AspNetCore.SignalR`: Merkez protokolleriyle ilgili Günlükler için, hub 'Ları etkinleştirme, yöntemleri çağırma ve hub ile ilgili diğer etkinlikler için.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="e2a6f-119">`Microsoft.AspNetCore.Http.Connections`: WebSockets, uzun yoklama, sunucu tarafından gönderilen olaylar ve alt düzey altyapı gibi aktarımlarıyla ilgili Günlükler için SignalR .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="e2a6f-120">Ayrıntılı günlükleri ' den etkinleştirmek için SignalR , `Debug` aşağıdaki öğeleri içindeki alt bölümüne ekleyerek, yukarıdaki ön ekleri *appSettings. JSON* dosyanızdaki düzeye yapılandırın `LogLevel` `Logging` :</span><span class="sxs-lookup"><span data-stu-id="e2a6f-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="e2a6f-121">Ayrıca, bu kodu yönteminizin içinde de yapılandırabilirsiniz `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e2a6f-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="e2a6f-122">JSON tabanlı yapılandırma kullanmıyorsanız, yapılandırma sisteminizde aşağıdaki yapılandırma değerlerini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="e2a6f-123">İç içe yapılandırma değerlerinin nasıl belirleneceğini belirlemek için yapılandırma sisteminizin belgelerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="e2a6f-124">Örneğin, ortam değişkenleri kullanılırken, `_` yerine iki karakter kullanılır `:` (örneğin, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="e2a6f-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="e2a6f-125">`Debug`Uygulamanız için daha ayrıntılı tanılama toplanırken düzeyin kullanılmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="e2a6f-126">`Trace`Düzey, çok düşük düzey Tanılamalar üretir ve uygulamanızdaki sorunları tanılamak için nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="e2a6f-127">Sunucu tarafı günlüklerine erişin</span><span class="sxs-lookup"><span data-stu-id="e2a6f-127">Access server-side logs</span></span>

<span data-ttu-id="e2a6f-128">Sunucu tarafı günlüklerine erişme, çalıştırdığınız ortama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="e2a6f-129">IIS dışında bir konsol uygulaması olarak</span><span class="sxs-lookup"><span data-stu-id="e2a6f-129">As a console app outside IIS</span></span>

<span data-ttu-id="e2a6f-130">Konsol uygulamasında çalıştırıyorsanız, [konsol günlükçüsü](xref:fundamentals/logging/index#console) varsayılan olarak etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="e2a6f-131">Günlükler konsolunda görünür.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="e2a6f-132">Visual Studio 'dan IIS Express içinde</span><span class="sxs-lookup"><span data-stu-id="e2a6f-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="e2a6f-133">Visual Studio **çıktı** penceresinde günlük çıktısını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="e2a6f-134">**ASP.NET Core Web sunucusu** açılır seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="e2a6f-135">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e2a6f-135">Azure App Service</span></span>

<span data-ttu-id="e2a6f-136">Azure App Service portalının **tanılama günlükleri** bölümünde **uygulama günlüğü (dosya sistemi)** seçeneğini etkinleştirin ve **düzeyini** olarak yapılandırın `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="e2a6f-137">Günlükler **günlük akış** hizmetinden ve App Service dosya sistemindeki günlüklerde kullanılabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="e2a6f-138">Daha fazla bilgi için bkz. [Azure günlük akışı](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="e2a6f-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="e2a6f-139">Diğer ortamlar</span><span class="sxs-lookup"><span data-stu-id="e2a6f-139">Other environments</span></span>

<span data-ttu-id="e2a6f-140">Uygulama başka bir ortama (örneğin, Docker, Kubernetes veya Windows hizmeti) dağıtılırsa, <xref:fundamentals/logging/index> ortama uygun günlük sağlayıcılarının nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz..</span><span class="sxs-lookup"><span data-stu-id="e2a6f-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="e2a6f-141">JavaScript istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="e2a6f-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e2a6f-142">İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e2a6f-143">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e2a6f-144">JavaScript istemcisini kullanırken, oturum açma seçeneklerini kullanarak şu `configureLogging` yöntemi kullanabilirsiniz `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e2a6f-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="e2a6f-145">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="e2a6f-146">Aşağıdaki tabloda JavaScript istemcisi için kullanılabilir olan günlük düzeyleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="e2a6f-147">Günlük düzeyinin bu değerlerden birine ayarlanması, bu düzeyde ve tabloda üzerindeki tüm düzeylerde günlüğe kaydetmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="e2a6f-148">Düzey</span><span class="sxs-lookup"><span data-stu-id="e2a6f-148">Level</span></span> | <span data-ttu-id="e2a6f-149">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e2a6f-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="e2a6f-150">Başlık: ' ASP.NET Core ' de günlüğe kaydetme ve tanılama SignalR : Açıklama: ' ASP.NET Core uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin SignalR . '</span><span class="sxs-lookup"><span data-stu-id="e2a6f-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e2a6f-151">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2a6f-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-152">'Blazor'</span></span>
- <span data-ttu-id="e2a6f-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-153">'Identity'</span></span>
- <span data-ttu-id="e2a6f-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2a6f-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-155">'Razor'</span></span>
- <span data-ttu-id="e2a6f-156">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2a6f-157">Başlık: ' ASP.NET Core ' de günlüğe kaydetme ve tanılama SignalR : Açıklama: ' ASP.NET Core uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin SignalR . '</span><span class="sxs-lookup"><span data-stu-id="e2a6f-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e2a6f-158">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2a6f-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-159">'Blazor'</span></span>
- <span data-ttu-id="e2a6f-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-160">'Identity'</span></span>
- <span data-ttu-id="e2a6f-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2a6f-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-162">'Razor'</span></span>
- <span data-ttu-id="e2a6f-163">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2a6f-164">Başlık: ' ASP.NET Core ' de günlüğe kaydetme ve tanılama SignalR : Açıklama: ' ASP.NET Core uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin SignalR . '</span><span class="sxs-lookup"><span data-stu-id="e2a6f-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e2a6f-165">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2a6f-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-166">'Blazor'</span></span>
- <span data-ttu-id="e2a6f-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-167">'Identity'</span></span>
- <span data-ttu-id="e2a6f-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2a6f-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2a6f-169">'Razor'</span></span>
- <span data-ttu-id="e2a6f-170">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-170">'SignalR' uid:</span></span> 

<span data-ttu-id="e2a6f-171">------ | | `None` | Hiçbir ileti günlüğe kaydedilmez.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="e2a6f-172">| | `Critical` | Uygulamanın tamamında bir hata olduğunu gösteren mesajlar.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="e2a6f-173">| | `Error` | Geçerli işlemdeki bir hatayı gösteren mesajlar.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="e2a6f-174">| | `Warning` | Önemli olmayan bir sorunu belirten mesajlar.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="e2a6f-175">| | `Information` | Bilgi iletileri.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="e2a6f-176">| | `Debug` | Tanılama iletileri hata ayıklama için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="e2a6f-177">| | `Trace` | Belirli sorunları tanılamak için tasarlanan çok ayrıntılı tanılama iletileri.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="e2a6f-178">Ayrıntı düzeyini yapılandırdıktan sonra, Günlükler tarayıcı konsoluna yazılır (veya bir NodeJS uygulamasında standart çıkış).</span><span class="sxs-lookup"><span data-stu-id="e2a6f-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="e2a6f-179">Günlükleri özel bir günlüğe kaydetme sistemine göndermek istiyorsanız, arabirimini uygulayan bir JavaScript nesnesi sağlayabilirsiniz `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="e2a6f-180">Uygulanması gereken tek yöntem `log` , olay düzeyini ve olayla ilişkili iletiyi alır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="e2a6f-181">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="e2a6f-182">.NET istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="e2a6f-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e2a6f-183">İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e2a6f-184">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e2a6f-185">.NET istemcisinden günlükleri almak için, `ConfigureLogging` üzerinde yöntemini kullanabilirsiniz `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="e2a6f-186">Bu, `ConfigureLogging` ve üzerindeki yöntemiyle aynı şekilde çalışmaktadır `WebHostBuilder` `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="e2a6f-187">ASP.NET Core ' de kullandığınız günlük sağlayıcılarını yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="e2a6f-188">Ancak, bireysel günlük sağlayıcıları için NuGet paketlerini el ile yükleyip etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="e2a6f-189">Konsol günlüğü</span><span class="sxs-lookup"><span data-stu-id="e2a6f-189">Console logging</span></span>

<span data-ttu-id="e2a6f-190">Konsol günlüğünü etkinleştirmek için [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="e2a6f-191">Ardından, `AddConsole` konsol günlükçüsü 'yi yapılandırmak için yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="e2a6f-192">Çıkış penceresi günlüğüne hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="e2a6f-192">Debug output window logging</span></span>

<span data-ttu-id="e2a6f-193">Günlükleri, Visual Studio 'daki **Çıkış** penceresine gitmek için de yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="e2a6f-194">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) paketini yükleyip `AddDebug` yöntemi kullanın:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="e2a6f-195">Diğer günlüğe kaydetme sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="e2a6f-195">Other logging providers</span></span>

SignalR<span data-ttu-id="e2a6f-196">Serilog, seq, NLog gibi diğer günlük sağlayıcılarını veya ile tümleştirilen diğer bir günlük sistemini destekler `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="e2a6f-197">Günlük sisteminiz bir sağlıyorsa `ILoggerProvider` , şunu kullanarak kaydedebilirsiniz `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="e2a6f-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="e2a6f-198">Denetim ayrıntı düzeyi</span><span class="sxs-lookup"><span data-stu-id="e2a6f-198">Control verbosity</span></span>

<span data-ttu-id="e2a6f-199">Uygulamanızdaki diğer yerlerden oturum açıyorsanız, varsayılan düzeyin olarak değiştirilmesi `Debug` çok ayrıntılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="e2a6f-200">Günlükler için günlüğe kaydetme düzeyini yapılandırmak üzere bir filtre kullanabilirsiniz SignalR .</span><span class="sxs-lookup"><span data-stu-id="e2a6f-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="e2a6f-201">Bu, sunucuda olduğu şekilde kodda yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="e2a6f-202">Ağ izlemeleri</span><span class="sxs-lookup"><span data-stu-id="e2a6f-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="e2a6f-203">Bir ağ izlemesi, uygulamanız tarafından gönderilen her iletinin tam içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="e2a6f-204">Ham ağ izlemelerini, üretim uygulamalarından GitHub gibi genel forumlara **hiçbir** şekilde yayımlayamazsınız.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e2a6f-205">Bir sorunla karşılaşırsanız, ağ izleme bazen yararlı olabilecek çok sayıda bilgi sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="e2a6f-206">Sorun izleyicimizde bir sorun oluşturacaksanız bu özellikle yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="e2a6f-207">Fiddler ile ağ izleme toplama (tercih edilen seçenek)</span><span class="sxs-lookup"><span data-stu-id="e2a6f-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="e2a6f-208">Bu yöntem tüm uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-208">This method works for all apps.</span></span>

<span data-ttu-id="e2a6f-209">Fiddler, HTTP izlemelerinin toplanması için çok güçlü bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="e2a6f-210">[Telerik.com/Fiddler](https://www.telerik.com/fiddler)adresinden yükleyip uygulamayı çalıştırın ve sorunu yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="e2a6f-211">Fiddler Windows için kullanılabilir ve macOS ve Linux için beta sürümleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="e2a6f-212">HTTPS kullanarak bağlanıyorsanız, Fiddler 'ın HTTPS trafiğinin şifresini çözebilmesini sağlamaya yönelik bazı ek adımlar vardır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="e2a6f-213">Daha ayrıntılı bilgi için bkz. [Fiddler belgeleri](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="e2a6f-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="e2a6f-214">İzlemeyi topladıktan sonra, **Dosya**  >  **Save**  >  **tüm oturumları** menü çubuğundan Kaydet ' i seçerek izlemeyi dışarı aktarabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler 'tan tüm oturumlar dışarı aktarılıyor](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="e2a6f-216">Tcpdump ile bir ağ izlemesi toplayın (yalnızca macOS ve Linux)</span><span class="sxs-lookup"><span data-stu-id="e2a6f-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="e2a6f-217">Bu yöntem tüm uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-217">This method works for all apps.</span></span>

<span data-ttu-id="e2a6f-218">Komut kabuğundan aşağıdaki komutu çalıştırarak, tcpdump kullanarak ham TCP izlemeleri toplayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="e2a6f-219">`root`Bir izin hatası alırsanız, komutuna sahip olmanız veya komuta önek uygulamanız gerekebilir `sudo` :</span><span class="sxs-lookup"><span data-stu-id="e2a6f-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="e2a6f-220">`[interface]`Yakalamak istediğiniz ağ arabirimiyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="e2a6f-221">Genellikle bu, `/dev/eth0` (Standart Ethernet arabiriminiz için) veya `/dev/lo0` (localhost trafiği için) gibi bir şeydir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="e2a6f-222">Daha fazla bilgi için bkz `tcpdump` . ana bilgisayar sisteminizdeki adam sayfası.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="e2a6f-223">Tarayıcıda bir ağ izlemesi toplayın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="e2a6f-224">Bu yöntem yalnızca tarayıcı tabanlı uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="e2a6f-225">Çoğu tarayıcı Geliştirici Araçları, tarayıcı ve sunucu arasında ağ etkinliğini yakalamanızı sağlayan bir "ağ" sekmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="e2a6f-226">Ancak, bu izlemeler WebSocket ve sunucu tarafından gönderilen olay iletilerini içermez.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="e2a6f-227">Bu taşımaları kullanıyorsanız, Fiddler veya TcpDump (aşağıda açıklanmıştır) gibi bir araç kullanmak daha iyi bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="e2a6f-228">Microsoft Edge ve Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e2a6f-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="e2a6f-229">(Yönergeler hem Edge hem de Internet Explorer için aynıdır)</span><span class="sxs-lookup"><span data-stu-id="e2a6f-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="e2a6f-230">Geliştirici araçlarını açmak için F12 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e2a6f-231">Ağ sekmesine tıklayın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-231">Click the Network Tab</span></span>
3. <span data-ttu-id="e2a6f-232">Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="e2a6f-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e2a6f-233">İzlemeyi "HAR" dosyası olarak dışarı aktarmak için araç çubuğundaki Kaydet simgesine tıklayın:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge geliştirme araçları Ağ sekmesinde Kaydet simgesi](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="e2a6f-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="e2a6f-235">Google Chrome</span></span>

1. <span data-ttu-id="e2a6f-236">Geliştirici araçlarını açmak için F12 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e2a6f-237">Ağ sekmesine tıklayın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-237">Click the Network Tab</span></span>
3. <span data-ttu-id="e2a6f-238">Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="e2a6f-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e2a6f-239">İstek listesinde herhangi bir yere sağ tıklayın ve "İçerikle HAR olarak Kaydet" i seçin:</span><span class="sxs-lookup"><span data-stu-id="e2a6f-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome geliştirme araçları Ağ sekmesinde "Içerik ile HAR olarak Kaydet" seçeneği](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="e2a6f-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e2a6f-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="e2a6f-242">Geliştirici araçlarını açmak için F12 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e2a6f-243">Ağ sekmesine tıklayın</span><span class="sxs-lookup"><span data-stu-id="e2a6f-243">Click the Network Tab</span></span>
3. <span data-ttu-id="e2a6f-244">Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="e2a6f-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e2a6f-245">İstek listesinde herhangi bir yere sağ tıklayın ve "tümünü HAR olarak Kaydet" i seçin</span><span class="sxs-lookup"><span data-stu-id="e2a6f-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox geliştirme araçları Ağ sekmesinde "tümünü HAR olarak Kaydet" seçeneği](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="e2a6f-247">GitHub sorunlarına tanılama dosyaları iliştirme</span><span class="sxs-lookup"><span data-stu-id="e2a6f-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="e2a6f-248">Tanılama dosyalarını, bir uzantıya sahip olacak şekilde yeniden adlandırarak `.txt` ve sonra sorunu üzerine sürükleyip bırakarak GitHub sorunlarına iliştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="e2a6f-249">Lütfen günlük dosyalarının veya ağ izlemelerinin içeriğini bir GitHub sorununa yapıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="e2a6f-250">Bu Günlükler ve izlemeler oldukça büyük olabilir ve GitHub genellikle bunları keser.</span><span class="sxs-lookup"><span data-stu-id="e2a6f-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Günlük dosyalarını bir GitHub sorununa sürükleme](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="e2a6f-252">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e2a6f-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
