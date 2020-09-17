---
title: .NET üzerinde gRPC 'de günlüğe kaydetme ve tanılama
author: jamesnk
description: .NET 'teki gRPC uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
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
uid: grpc/diagnostics
ms.openlocfilehash: 7d2da20d04b93ebcd16fb58a4b74b5b67d37bd72
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722929"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="26d3b-103">.NET üzerinde gRPC 'de günlüğe kaydetme ve tanılama</span><span class="sxs-lookup"><span data-stu-id="26d3b-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="26d3b-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="26d3b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="26d3b-105">Bu makale, sorunları gidermeye yardımcı olmak için bir gRPC uygulamasından tanılamayı toplamaya yönelik rehberlik sağlar.</span><span class="sxs-lookup"><span data-stu-id="26d3b-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="26d3b-106">Ele alınan konular:</span><span class="sxs-lookup"><span data-stu-id="26d3b-106">Topics covered include:</span></span>

* <span data-ttu-id="26d3b-107">**Günlüğe kaydetme** - [.NET Core günlüğe](xref:fundamentals/logging/index)yazma ile yazılan yapılandırılmış Günlükler.</span><span class="sxs-lookup"><span data-stu-id="26d3b-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="26d3b-108"><xref:Microsoft.Extensions.Logging.ILogger> , uygulama çerçeveleri tarafından, günlükleri yazmak için ve kullanıcılar tarafından bir uygulamada kendi günlüğe kaydetme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="26d3b-109">**İzleme** -ve kullanılarak yazılmış bir işlemle ilgili olaylar `DiaganosticSource` `Activity` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="26d3b-110">Tanılama kaynağından alınan izlemeler, [Application Insights](/azure/azure-monitor/app/asp-net-core) ve [opentelemetri](https://github.com/open-telemetry/opentelemetry-dotnet)gibi kitaplıklara göre uygulama telemetrisini toplamak için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="26d3b-111">**Ölçümler** -saniye başına isteklerin zaman aralıklarıyla veri ölçülerinin temsili.</span><span class="sxs-lookup"><span data-stu-id="26d3b-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="26d3b-112">Ölçümler kullanılarak dağıtılır `EventCounter` ve [DotNet-Counters](/dotnet/core/diagnostics/dotnet-counters) komut satırı aracı kullanılarak veya [Application Insights](/azure/azure-monitor/app/eventcounters)ile gözlemlenebilir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="26d3b-113">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="26d3b-113">Logging</span></span>

<span data-ttu-id="26d3b-114">gRPC Hizmetleri ve gRPC istemcisi [.NET Core günlüğü](xref:fundamentals/logging/index)kullanarak yazma günlükleri.</span><span class="sxs-lookup"><span data-stu-id="26d3b-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="26d3b-115">Günlüklerde, uygulamalarınızda beklenmeyen davranışa hata ayıklamanız gerektiğinde başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="26d3b-116">gRPC Hizmetleri günlüğü</span><span class="sxs-lookup"><span data-stu-id="26d3b-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="26d3b-117">Sunucu tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="26d3b-118">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="26d3b-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="26d3b-119">GRPC Hizmetleri ASP.NET Core üzerinde barındırıldığından, bu, ASP.NET Core günlük sistemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="26d3b-120">Varsayılan yapılandırmada, gRPC çok az bilgiyi günlüğe kaydeder, ancak bu yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="26d3b-121">ASP.NET Core günlüğü yapılandırma hakkında ayrıntılar için [ASP.NET Core günlüğe kaydetme](xref:fundamentals/logging/index#configuration) hakkındaki belgelere bakın.</span><span class="sxs-lookup"><span data-stu-id="26d3b-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="26d3b-122">gRPC, kategori altına Günlükler ekler `Grpc` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="26d3b-123">GRPC 'den ayrıntılı günlükleri etkinleştirmek için, `Grpc` `Debug` aşağıdaki öğeleri içindeki alt bölümüne ekleyerek, ön ekleri dosyadaki *appsettings.js* düzeyine yapılandırın `LogLevel` `Logging` :</span><span class="sxs-lookup"><span data-stu-id="26d3b-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="26d3b-124">Bunu, *Startup.cs* içinde şu şekilde de yapılandırabilirsiniz `ConfigureLogging` :</span><span class="sxs-lookup"><span data-stu-id="26d3b-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="26d3b-125">JSON tabanlı yapılandırma kullanmıyorsanız, yapılandırma sisteminizde aşağıdaki yapılandırma değerini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="26d3b-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="26d3b-126">İç içe yapılandırma değerlerinin nasıl belirleneceğini belirlemek için yapılandırma sisteminizin belgelerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="26d3b-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="26d3b-127">Örneğin, ortam değişkenleri kullanılırken, `_` yerine iki karakter kullanılır `:` (örneğin, `Logging__LogLevel__Grpc` ).</span><span class="sxs-lookup"><span data-stu-id="26d3b-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="26d3b-128">`Debug`Uygulamanız için daha ayrıntılı tanılama toplanırken düzeyin kullanılmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="26d3b-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="26d3b-129">`Trace`Düzey, çok düşük düzey Tanılamalar üretir ve uygulamanızdaki sorunları tanılamak için nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="26d3b-130">Örnek günlüğe kaydetme çıkışı</span><span class="sxs-lookup"><span data-stu-id="26d3b-130">Sample logging output</span></span>

<span data-ttu-id="26d3b-131">Aşağıda, `Debug` bir gRPC hizmeti düzeyinde konsol çıkışının bir örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="26d3b-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="26d3b-132">Sunucu tarafı günlüklerine erişin</span><span class="sxs-lookup"><span data-stu-id="26d3b-132">Access server-side logs</span></span>

<span data-ttu-id="26d3b-133">Sunucu tarafı günlüklerine erişme, çalıştırdığınız ortama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="26d3b-134">Konsol uygulaması olarak</span><span class="sxs-lookup"><span data-stu-id="26d3b-134">As a console app</span></span>

<span data-ttu-id="26d3b-135">Konsol uygulamasında çalıştırıyorsanız, [konsol günlükçüsü](xref:fundamentals/logging/index#console) varsayılan olarak etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="26d3b-136">gRPC günlükleri konsolunda görünür.</span><span class="sxs-lookup"><span data-stu-id="26d3b-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="26d3b-137">Diğer ortamlar</span><span class="sxs-lookup"><span data-stu-id="26d3b-137">Other environments</span></span>

<span data-ttu-id="26d3b-138">Uygulama başka bir ortama (örneğin, Docker, Kubernetes veya Windows hizmeti) dağıtılırsa, <xref:fundamentals/logging/index> ortama uygun günlük sağlayıcılarının nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz..</span><span class="sxs-lookup"><span data-stu-id="26d3b-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="26d3b-139">gRPC istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="26d3b-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="26d3b-140">İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="26d3b-141">Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.</span><span class="sxs-lookup"><span data-stu-id="26d3b-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="26d3b-142">.NET istemcisinden günlükleri almak için, `GrpcChannelOptions.LoggerFactory` özelliği istemci kanalının oluşturulduğu zaman ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="26d3b-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="26d3b-143">Bir ASP.NET Core uygulamasından gRPC hizmetini arıyorsanız, günlükçü fabrikası bağımlılık ekleme (DI) ' dan çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="26d3b-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="26d3b-144">İstemci günlüğünü etkinleştirmenin alternatif bir yolu, istemci oluşturmak için [GRPC istemci fabrikası](xref:grpc/clientfactory) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="26d3b-145">İstemci fabrikasına kayıtlı ve dı 'den çözümlenen bir gRPC istemcisi, otomatik olarak uygulamanın yapılandırılmış günlüğünü kullanacaktır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="26d3b-146">Uygulamanız DI kullanıyorsa, `ILoggerFactory` [Loggerfactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)ile yeni bir örnek oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="26d3b-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="26d3b-147">Bu yönteme erişmek için [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) paketini uygulamanıza ekleyin.</span><span class="sxs-lookup"><span data-stu-id="26d3b-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="26d3b-148">gRPC istemci günlüğü kapsamları</span><span class="sxs-lookup"><span data-stu-id="26d3b-148">gRPC client log scopes</span></span>

<span data-ttu-id="26d3b-149">GRPC istemcisi, gRPC çağrısı sırasında yapılan günlüklere bir [günlüğe kaydetme kapsamı](../fundamentals/logging/index.md#log-scopes) ekler.</span><span class="sxs-lookup"><span data-stu-id="26d3b-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="26d3b-150">Kapsamda gRPC çağrısıyla ilgili meta veriler vardır:</span><span class="sxs-lookup"><span data-stu-id="26d3b-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="26d3b-151">**Grpcmethodtype** -GRPC Yöntem türü.</span><span class="sxs-lookup"><span data-stu-id="26d3b-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="26d3b-152">Olası değerler, enum 'tan adlardır `Grpc.Core.MethodType` , örneğin Birli</span><span class="sxs-lookup"><span data-stu-id="26d3b-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="26d3b-153">**Grpcuri** -GRPC yönteminin göreli URI 'si; Örneğin,/bir. Greeter/Saymerhaba s</span><span class="sxs-lookup"><span data-stu-id="26d3b-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="26d3b-154">Örnek günlüğe kaydetme çıkışı</span><span class="sxs-lookup"><span data-stu-id="26d3b-154">Sample logging output</span></span>

<span data-ttu-id="26d3b-155">Aşağıda, `Debug` bir gRPC istemcisinin düzeyindeki konsol çıkışının bir örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="26d3b-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="26d3b-156">İzleme</span><span class="sxs-lookup"><span data-stu-id="26d3b-156">Tracing</span></span>

<span data-ttu-id="26d3b-157">gRPC Hizmetleri ve gRPC istemcisi, [Diagnosticsource](/dotnet/api/system.diagnostics.diagnosticsource) ve [Activity](/dotnet/api/system.diagnostics.activity)kullanarak GRPC çağrıları hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="26d3b-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="26d3b-158">.NET gRPC, bir gRPC çağrısını temsil eden bir etkinlik kullanır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="26d3b-159">İzleme olayları, gRPC çağrısı etkinliğinin başlangıcında ve durdurulduğunda tanılama kaynağına yazılır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="26d3b-160">İzleme, gRPC akış çağrılarının kullanım ömrü boyunca iletiler gönderildiğinde hakkında bilgi yakalamaz.</span><span class="sxs-lookup"><span data-stu-id="26d3b-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="26d3b-161">gRPC hizmeti izleme</span><span class="sxs-lookup"><span data-stu-id="26d3b-161">gRPC service tracing</span></span>

<span data-ttu-id="26d3b-162">gRPC Hizmetleri, gelen HTTP istekleriyle ilgili olayları raporlayan ASP.NET Core barındırılır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="26d3b-163">gRPC 'ye özgü meta veriler, ASP.NET Core sağladığı mevcut HTTP istek tanımasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="26d3b-164">Tanılama kaynağı adı `Microsoft.AspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="26d3b-165">Etkinlik adı `Microsoft.AspNetCore.Hosting.HttpRequestIn` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="26d3b-166">GRPC çağrısı tarafından çağrılan gRPC yönteminin adı, adı olan bir etiket olarak eklenir `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="26d3b-167">Bir etiket olarak tamamlandığında gRPC çağrısının durum kodu, adıyla birlikte eklenir `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="26d3b-168">gRPC istemci izleme</span><span class="sxs-lookup"><span data-stu-id="26d3b-168">gRPC client tracing</span></span>

<span data-ttu-id="26d3b-169">.NET gRPC istemcisi, `HttpClient` GRPC çağrıları yapmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="26d3b-170">`HttpClient`, Tanılama olaylarını yazmakla birlikte, .net GRPC istemcisi özel bir tanılama kaynağı, etkinlik ve olaylar sağlayarak bir GRPC çağrısıyla ilgili tüm bilgilerin toplanmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="26d3b-171">Tanılama kaynağı adı `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="26d3b-172">Etkinlik adı `Grpc.Net.Client.GrpcOut` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="26d3b-173">GRPC çağrısı tarafından çağrılan gRPC yönteminin adı, adı olan bir etiket olarak eklenir `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="26d3b-174">Bir etiket olarak tamamlandığında gRPC çağrısının durum kodu, adıyla birlikte eklenir `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="26d3b-175">İzleme toplanıyor</span><span class="sxs-lookup"><span data-stu-id="26d3b-175">Collecting tracing</span></span>

<span data-ttu-id="26d3b-176">Kullanmanın en kolay yolu, `DiagnosticSource` uygulamanızda [Application Insights](/azure/azure-monitor/app/asp-net-core) veya [opentelemetri](https://github.com/open-telemetry/opentelemetry-dotnet) gibi bir telemetri kitaplığı yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="26d3b-177">Bu kitaplık, gRPC ile diğer uygulama telemetrisini çağıran bilgileri işleyecek.</span><span class="sxs-lookup"><span data-stu-id="26d3b-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="26d3b-178">İzleme Application Insights gibi bir yönetilen hizmette görüntülenebilir veya kendi dağıtılmış izleme sisteminizi çalıştırmayı tercih edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="26d3b-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="26d3b-179">Opentelemetri, izleme verilerinin [Caeger](https://www.jaegertracing.io/) ve [zipy](https://zipkin.io/)'ye verilmesini destekler.</span><span class="sxs-lookup"><span data-stu-id="26d3b-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="26d3b-180">`DiagnosticSource` , kullanarak koddaki izleme olaylarını tüketebilir `DiagnosticListener` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="26d3b-181">Bir tanılama kaynağını kodla dinleme hakkında daha fazla bilgi için bkz. [diagnosticsource Kullanıcı Kılavuzu](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="26d3b-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="26d3b-182">Telemetri kitaplıkları, şu anda gRPC 'ye özgü telemetri yakalayamaz `Grpc.Net.Client.GrpcOut` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="26d3b-183">Telemetri kitaplıklarını geliştirmek için çalış bu izlemeyi devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="26d3b-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="26d3b-184">Ölçümler</span><span class="sxs-lookup"><span data-stu-id="26d3b-184">Metrics</span></span>

<span data-ttu-id="26d3b-185">Ölçümler, zaman aralıklarıyla veri ölçülerinin bir gösterimidir (örneğin, saniye başına istek).</span><span class="sxs-lookup"><span data-stu-id="26d3b-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="26d3b-186">Ölçüm verileri, yüksek düzeyde bir uygulamanın durumunun gözlemde yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="26d3b-187">.NET gRPC ölçümleri kullanılarak dağıtılır `EventCounter` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="26d3b-188">gRPC hizmeti ölçümleri</span><span class="sxs-lookup"><span data-stu-id="26d3b-188">gRPC service metrics</span></span>

<span data-ttu-id="26d3b-189">gRPC sunucu ölçümleri `Grpc.AspNetCore.Server` olay kaynağında raporlanır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="26d3b-190">Ad</span><span class="sxs-lookup"><span data-stu-id="26d3b-190">Name</span></span>                      | <span data-ttu-id="26d3b-191">Açıklama</span><span class="sxs-lookup"><span data-stu-id="26d3b-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="26d3b-192">Toplam çağrı sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="26d3b-193">Geçerli çağrılar</span><span class="sxs-lookup"><span data-stu-id="26d3b-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="26d3b-194">Toplam başarısız çağrı sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="26d3b-195">Toplam çağrı son tarihi aşıldı</span><span class="sxs-lookup"><span data-stu-id="26d3b-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="26d3b-196">Gönderilen toplam Ileti sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="26d3b-197">Alınan toplam Ileti sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="26d3b-198">Uygulanmayan Toplam çağrı sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="26d3b-199">ASP.NET Core Ayrıca olay kaynağı üzerinde kendi ölçümlerini de sağlar `Microsoft.AspNetCore.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="26d3b-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="26d3b-200">gRPC istemci ölçümleri</span><span class="sxs-lookup"><span data-stu-id="26d3b-200">gRPC client metrics</span></span>

<span data-ttu-id="26d3b-201">gRPC istemci ölçümleri `Grpc.Net.Client` olay kaynağında raporlanır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="26d3b-202">Ad</span><span class="sxs-lookup"><span data-stu-id="26d3b-202">Name</span></span>                      | <span data-ttu-id="26d3b-203">Açıklama</span><span class="sxs-lookup"><span data-stu-id="26d3b-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="26d3b-204">Toplam çağrı sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="26d3b-205">Geçerli çağrılar</span><span class="sxs-lookup"><span data-stu-id="26d3b-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="26d3b-206">Toplam başarısız çağrı sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="26d3b-207">Toplam çağrı son tarihi aşıldı</span><span class="sxs-lookup"><span data-stu-id="26d3b-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="26d3b-208">Gönderilen toplam Ileti sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="26d3b-209">Alınan toplam Ileti sayısı</span><span class="sxs-lookup"><span data-stu-id="26d3b-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="26d3b-210">Ölçümleri gözlemleyin</span><span class="sxs-lookup"><span data-stu-id="26d3b-210">Observe metrics</span></span>

<span data-ttu-id="26d3b-211">[DotNet sayaçları](/dotnet/core/diagnostics/dotnet-counters) , geçici sistem durumu izleme ve ilk düzey performans araştırması için bir performans izleme aracıdır.</span><span class="sxs-lookup"><span data-stu-id="26d3b-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="26d3b-212">Bir .NET uygulamasını `Grpc.AspNetCore.Server` ya da `Grpc.Net.Client` sağlayıcı adıyla birlikte izleyin.</span><span class="sxs-lookup"><span data-stu-id="26d3b-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="26d3b-213">GRPC ölçümlerini gözlemlemeye yönelik başka bir yol da Application Insights [Microsoft. ApplicationInsights. EventCounterCollector paketini](/azure/azure-monitor/app/eventcounters)kullanarak sayaç verilerini yakalemektir.</span><span class="sxs-lookup"><span data-stu-id="26d3b-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="26d3b-214">Kurulumdan sonra, Application Insights çalışma zamanında ortak .NET sayaçlarını toplar.</span><span class="sxs-lookup"><span data-stu-id="26d3b-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="26d3b-215">gRPC 'nin sayaçları varsayılan olarak toplanmaz, ancak uygulama öngörüleri [ek sayaçlar içerecek şekilde özelleştirilebilir](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="26d3b-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="26d3b-216">*Startup.cs*Içinde toplanacak uygulama öngörüleri Için GRPC sayaçlarını belirtin:</span><span class="sxs-lookup"><span data-stu-id="26d3b-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="26d3b-217">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="26d3b-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>