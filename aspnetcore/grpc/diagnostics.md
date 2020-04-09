---
title: .NET'te gRPC'de günlük ve tanılama
author: jamesnk
description: .NET'teki gRPC uygulamanızdan tanılamayı nasıl topladığınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417512"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="c7374-103">.NET'te gRPC'de günlük ve tanılama</span><span class="sxs-lookup"><span data-stu-id="c7374-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="c7374-104">Yazar: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c7374-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c7374-105">Bu makalede, sorunları gidermeye yardımcı olmak için bir gRPC uygulamasından tanılama toplamak için kılavuz sağlar.</span><span class="sxs-lookup"><span data-stu-id="c7374-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="c7374-106">Ele alınan konular:</span><span class="sxs-lookup"><span data-stu-id="c7374-106">Topics covered include:</span></span>

* <span data-ttu-id="c7374-107">**Günlük** - [.NET Core günlük](xref:fundamentals/logging/index)lerine yazılmış yapılandırılmış günlükler.</span><span class="sxs-lookup"><span data-stu-id="c7374-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c7374-108"><xref:Microsoft.Extensions.Logging.ILogger>günlükleri yazmak için uygulama çerçeveleri tarafından ve kullanıcılar tarafından bir uygulamada kendi oturum açma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7374-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="c7374-109">**İzleme** - Kullanılarak `DiaganosticSource` yazılan bir işlemle ilgili olaylar ve `Activity`.</span><span class="sxs-lookup"><span data-stu-id="c7374-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="c7374-110">Tanı kaynağından gelen izler genellikle [Uygulama Öngörüleri](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ve [OpenTelemetri](https://github.com/open-telemetry/opentelemetry-dotnet)gibi kitaplıklar tarafından uygulama telemetrisini toplamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7374-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="c7374-111">Ölçümler - Veri ölçülerinin zaman aralıkları, örneğin, saniyedeki **istekler** üzerinden gösterimi.</span><span class="sxs-lookup"><span data-stu-id="c7374-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="c7374-112">Ölçümler kullanılarak `EventCounter` yayılır ve [dotnet sayaçları](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) komut satırı aracı kullanılarak veya [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)ile gözlemlenebilir.</span><span class="sxs-lookup"><span data-stu-id="c7374-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="c7374-113">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="c7374-113">Logging</span></span>

<span data-ttu-id="c7374-114">gRPC hizmetleri ve gRPC istemcisi [.NET Core günlük](xref:fundamentals/logging/index)lerini kullanarak günlükleri yazmak.</span><span class="sxs-lookup"><span data-stu-id="c7374-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c7374-115">Günlükler, uygulamalarınızda beklenmeyen davranışları hata ayıklamanız gerektiğinde başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="c7374-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="c7374-116">gRPC hizmetleri günlüğü</span><span class="sxs-lookup"><span data-stu-id="c7374-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="c7374-117">Sunucu tarafındaki günlükler uygulamanızdan hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c7374-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c7374-118">**Üretim** uygulamalarından GitHub gibi genel forumlara asla ham günlükler göndermeyin.</span><span class="sxs-lookup"><span data-stu-id="c7374-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c7374-119">gRPC hizmetleri ASP.NET Core'da barındırılan bu yana, ASP.NET Core günlük sistemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="c7374-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="c7374-120">Varsayılan yapılandırmada, gRPC çok az bilgi kaydeder, ancak bu yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c7374-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="c7374-121">ASP.NET Core [günlük](xref:fundamentals/logging/index#configuration) yapılandırma sı hakkında ayrıntılı bilgi için ASP.NET Core günlük üzerindeki belgelere bakın.</span><span class="sxs-lookup"><span data-stu-id="c7374-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="c7374-122">gRPC kategorisi altında `Grpc` günlükleri ekler.</span><span class="sxs-lookup"><span data-stu-id="c7374-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="c7374-123">gRPC'den ayrıntılı günlükleri etkinleştirmek `Grpc` için, önekleri *appsettings.json* dosyanızdaki `Debug` düzeye doğru, `LogLevel` aşağıdaki öğeleri `Logging`aşağıdaki alt bölüme ekleyerek yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="c7374-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="c7374-124">Ayrıca bu *Startup.cs* ile `ConfigureLogging`yapılandırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c7374-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="c7374-125">JSON tabanlı yapılandırma kullanmıyorsanız, yapılandırma sisteminizde aşağıdaki yapılandırma değerini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c7374-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="c7374-126">İç içe geçme yapılandırma değerlerini nasıl belirtin belirlemek için yapılandırma sisteminizin belgelerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="c7374-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="c7374-127">Örneğin, ortam değişkenleri kullanılırken, `_` `:` (örneğin,) `Logging__LogLevel__Grpc`yerine iki karakter kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7374-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="c7374-128">Uygulamanız için `Debug` daha ayrıntılı tanılama lar toplarken düzeyi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="c7374-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="c7374-129">Düzey `Trace` çok düşük düzeyde tanılama üretir ve uygulamanızdaki sorunları tanılamak için nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c7374-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="c7374-130">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="c7374-130">Sample logging output</span></span>

<span data-ttu-id="c7374-131">Aşağıda, bir gRPC `Debug` hizmeti düzeyinde konsol çıkışına bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c7374-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="c7374-132">Sunucu tarafındaki günlüklere erişin</span><span class="sxs-lookup"><span data-stu-id="c7374-132">Access server-side logs</span></span>

<span data-ttu-id="c7374-133">Sunucu tarafındaki günlüklere nasıl eriştüğün, içinde çalıştığınız ortama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="c7374-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="c7374-134">Konsol uygulaması olarak</span><span class="sxs-lookup"><span data-stu-id="c7374-134">As a console app</span></span>

<span data-ttu-id="c7374-135">Bir konsol uygulamasında çalışıyorsanız, [Konsol kaydedicisi](xref:fundamentals/logging/index#console-provider) varsayılan olarak etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="c7374-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="c7374-136">konsolda gRPC günlükleri görünür.</span><span class="sxs-lookup"><span data-stu-id="c7374-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="c7374-137">Diğer ortamlar</span><span class="sxs-lookup"><span data-stu-id="c7374-137">Other environments</span></span>

<span data-ttu-id="c7374-138">Uygulama başka bir ortama (örneğin, Docker, Kubernetes veya Windows <xref:fundamentals/logging/index> Service) dağıtılırsa, ortama uygun günlük sağlayıcılarını nasıl yapılandırılabilirsiniz hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c7374-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="c7374-139">gRPC istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="c7374-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="c7374-140">İstemci tarafı günlükleri uygulamanızdan hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c7374-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c7374-141">**Üretim** uygulamalarından GitHub gibi genel forumlara asla ham günlükler göndermeyin.</span><span class="sxs-lookup"><span data-stu-id="c7374-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c7374-142">.NET istemcisinden günlük almak için, istemcinin `GrpcChannelOptions.LoggerFactory` kanalı oluşturulduğunda özelliği ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c7374-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="c7374-143">Bir ASP.NET Core uygulamasından gRPC hizmetini arıyorsanız, logger fabrikası bağımlılık enjeksiyonundan (DI) çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="c7374-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="c7374-144">İstemci günlüğe kaydetmeyi etkinleştirmek için alternatif bir yol istemci oluşturmak için [gRPC istemci fabrikasını](xref:grpc/clientfactory) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="c7374-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="c7374-145">İstemci fabrikasına kayıtlı ve DI'den çözümlenen bir gRPC istemcisi, uygulamanın yapılandırılmış günlüğe kaydetmesini otomatik olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="c7374-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="c7374-146">Uygulamanız DI kullanmıyorsa `ILoggerFactory` [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)ile yeni bir örnek oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c7374-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="c7374-147">Bu yönteme erişmek için uygulamanıza [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c7374-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="c7374-148">gRPC istemci günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="c7374-148">gRPC client log scopes</span></span>

<span data-ttu-id="c7374-149">gRPC istemcisi, gRPC araması sırasında yapılan günlüklere günlük [kapsamı](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) ekler.</span><span class="sxs-lookup"><span data-stu-id="c7374-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="c7374-150">Kapsam gRPC çağrısı ile ilgili meta verilere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="c7374-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="c7374-151">**GrpcMethodType** - gRPC yöntem türü.</span><span class="sxs-lookup"><span data-stu-id="c7374-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="c7374-152">Olası değerler enum adlarıdır, `Grpc.Core.MethodType` örneğin Unary</span><span class="sxs-lookup"><span data-stu-id="c7374-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="c7374-153">**GrpcUri** - gRPC yönteminin göreceli URI, örneğin / selamlıyorum. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="c7374-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="c7374-154">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="c7374-154">Sample logging output</span></span>

<span data-ttu-id="c7374-155">Aşağıda, bir gRPC `Debug` istemcisi düzeyinde konsol çıktısı örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c7374-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="c7374-156">İzleme</span><span class="sxs-lookup"><span data-stu-id="c7374-156">Tracing</span></span>

<span data-ttu-id="c7374-157">gRPC hizmetleri ve gRPC istemcisi [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) ve [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)kullanarak gRPC aramaları hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c7374-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="c7374-158">.NET gRPC, gRPC çağrısını temsil etmek için bir etkinlik kullanır.</span><span class="sxs-lookup"><span data-stu-id="c7374-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="c7374-159">İzleme olayları gRPC arama etkinliğinin başında ve durağında tanı kaynağına yazılır.</span><span class="sxs-lookup"><span data-stu-id="c7374-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="c7374-160">İzleme, gRPC akış çağrılarının kullanım ömrü boyunca iletilerin ne zaman gönderildiği hakkında bilgi yakalamaz.</span><span class="sxs-lookup"><span data-stu-id="c7374-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="c7374-161">gRPC hizmet izleme</span><span class="sxs-lookup"><span data-stu-id="c7374-161">gRPC service tracing</span></span>

<span data-ttu-id="c7374-162">gRPC hizmetleri, gelen HTTP istekleriyle ilgili olayları bildiren ASP.NET Core'da barındırılır.</span><span class="sxs-lookup"><span data-stu-id="c7374-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="c7374-163">ASP.NET Core'un sağladığı mevcut HTTP isteği tanılamalarına gRPC'ye özgü meta veriler eklenir.</span><span class="sxs-lookup"><span data-stu-id="c7374-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="c7374-164">Tanılama kaynak `Microsoft.AspNetCore`adıdır.</span><span class="sxs-lookup"><span data-stu-id="c7374-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="c7374-165">Etkinlik adı `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="c7374-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="c7374-166">gRPC çağrısı tarafından çağrılan gRPC yönteminin adı, ad `grpc.method`ile etiket olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="c7374-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="c7374-167">Tamamlandığında gRPC çağrısının durum kodu adı `grpc.status_code`ile bir etiket olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="c7374-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="c7374-168">gRPC istemci izleme</span><span class="sxs-lookup"><span data-stu-id="c7374-168">gRPC client tracing</span></span>

<span data-ttu-id="c7374-169">.NET gRPC istemcisi gRPC aramaları yapmak için kullanır. `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="c7374-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="c7374-170">Tanılama olayları yazsa da, `HttpClient` .NET gRPC istemcisi özel bir tanılama kaynağı, etkinlik ve olaylar sağlar, böylece bir gRPC çağrısı hakkında tam bilgi toplanabilir.</span><span class="sxs-lookup"><span data-stu-id="c7374-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="c7374-171">Tanılama kaynak `Grpc.Net.Client`adıdır.</span><span class="sxs-lookup"><span data-stu-id="c7374-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="c7374-172">Etkinlik adı `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="c7374-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="c7374-173">gRPC çağrısı tarafından çağrılan gRPC yönteminin adı, ad `grpc.method`ile etiket olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="c7374-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="c7374-174">Tamamlandığında gRPC çağrısının durum kodu adı `grpc.status_code`ile bir etiket olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="c7374-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="c7374-175">İzleme yi toplama</span><span class="sxs-lookup"><span data-stu-id="c7374-175">Collecting tracing</span></span>

<span data-ttu-id="c7374-176">En kolay kullanım `DiagnosticSource` yolu, [uygulamanızda Uygulama Öngörüleri](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) veya [OpenTelemetri](https://github.com/open-telemetry/opentelemetry-dotnet) gibi bir telemetri kitaplığını yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="c7374-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="c7374-177">Kitaplık, diğer uygulama telemetrisi ile birlikte gRPC çağrıları hakkındaki bilgileri işleyecek.</span><span class="sxs-lookup"><span data-stu-id="c7374-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="c7374-178">İzleme, Uygulama Öngörüleri gibi yönetilen bir hizmette görüntülenebilir veya kendi dağıtılmış izleme sisteminizi çalıştırmayı seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c7374-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="c7374-179">OpenTelemetry [Jaeger](https://www.jaegertracing.io/) ve [Zipkin](https://zipkin.io/)veri izleme dışa aktarAn destekler.</span><span class="sxs-lookup"><span data-stu-id="c7374-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="c7374-180">`DiagnosticSource`kullanarak `DiagnosticListener`koddaki olayları izleme tüketebilir.</span><span class="sxs-lookup"><span data-stu-id="c7374-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="c7374-181">Kodlu bir tanı kaynağını dinleme hakkında bilgi için [DiagnosticSource kullanıcı kılavuzuna](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)bakın.</span><span class="sxs-lookup"><span data-stu-id="c7374-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="c7374-182">Telemetri kitaplıkları şu anda gRPC'ye özgü `Grpc.Net.Client.GrpcOut` telemetriyi yakalayamıyor.</span><span class="sxs-lookup"><span data-stu-id="c7374-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="c7374-183">Bu izleme yakalama telemetri kütüphaneleri geliştirmek için çalışmalar devam etmektedir.</span><span class="sxs-lookup"><span data-stu-id="c7374-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="c7374-184">Ölçümler</span><span class="sxs-lookup"><span data-stu-id="c7374-184">Metrics</span></span>

<span data-ttu-id="c7374-185">Ölçümler, örneğin saniyedeki istekler gibi zaman aralıklarına göre veri ölçülerinin bir temsilidir.</span><span class="sxs-lookup"><span data-stu-id="c7374-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="c7374-186">Ölçümler verileri, bir uygulamanın durumunun üst düzeyde gözlemlemesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c7374-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="c7374-187">.NET gRPC ölçümleri kullanılarak `EventCounter`yayılır.</span><span class="sxs-lookup"><span data-stu-id="c7374-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="c7374-188">gRPC hizmet ölçümleri</span><span class="sxs-lookup"><span data-stu-id="c7374-188">gRPC service metrics</span></span>

<span data-ttu-id="c7374-189">gRPC sunucu ölçümleri `Grpc.AspNetCore.Server` olay kaynağında raporlanır.</span><span class="sxs-lookup"><span data-stu-id="c7374-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="c7374-190">Adı</span><span class="sxs-lookup"><span data-stu-id="c7374-190">Name</span></span>                      | <span data-ttu-id="c7374-191">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c7374-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="c7374-192">Toplam Aramalar</span><span class="sxs-lookup"><span data-stu-id="c7374-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="c7374-193">Güncel Aramalar</span><span class="sxs-lookup"><span data-stu-id="c7374-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="c7374-194">Toplam Aramalar Başarısız Oldu</span><span class="sxs-lookup"><span data-stu-id="c7374-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="c7374-195">Toplam Arama Süresi Aşıldı</span><span class="sxs-lookup"><span data-stu-id="c7374-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="c7374-196">Gönderilen Toplam İleti Sayısı</span><span class="sxs-lookup"><span data-stu-id="c7374-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="c7374-197">Alınan Toplam İleti</span><span class="sxs-lookup"><span data-stu-id="c7374-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="c7374-198">Uygulanmamış Toplam Aramalar</span><span class="sxs-lookup"><span data-stu-id="c7374-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="c7374-199">ASP.NET Core da olay kaynağı `Microsoft.AspNetCore.Hosting` üzerinde kendi ölçümleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="c7374-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="c7374-200">gRPC istemci ölçümleri</span><span class="sxs-lookup"><span data-stu-id="c7374-200">gRPC client metrics</span></span>

<span data-ttu-id="c7374-201">gRPC istemci ölçümleri `Grpc.Net.Client` olay kaynağında raporlanır.</span><span class="sxs-lookup"><span data-stu-id="c7374-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="c7374-202">Adı</span><span class="sxs-lookup"><span data-stu-id="c7374-202">Name</span></span>                      | <span data-ttu-id="c7374-203">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c7374-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="c7374-204">Toplam Aramalar</span><span class="sxs-lookup"><span data-stu-id="c7374-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="c7374-205">Güncel Aramalar</span><span class="sxs-lookup"><span data-stu-id="c7374-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="c7374-206">Toplam Aramalar Başarısız Oldu</span><span class="sxs-lookup"><span data-stu-id="c7374-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="c7374-207">Toplam Arama Süresi Aşıldı</span><span class="sxs-lookup"><span data-stu-id="c7374-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="c7374-208">Gönderilen Toplam İleti Sayısı</span><span class="sxs-lookup"><span data-stu-id="c7374-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="c7374-209">Alınan Toplam İleti</span><span class="sxs-lookup"><span data-stu-id="c7374-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="c7374-210">Ölçümleri gözlemle</span><span class="sxs-lookup"><span data-stu-id="c7374-210">Observe metrics</span></span>

<span data-ttu-id="c7374-211">[dotnet sayaçları,](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) özel sistem durumu izleme ve birinci düzey performans araştırması için bir performans izleme aracıdır.</span><span class="sxs-lookup"><span data-stu-id="c7374-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="c7374-212">Bir .NET uygulamasını `Grpc.AspNetCore.Server` sağlayıcı `Grpc.Net.Client` adı olarak veya sağlayıcı adı olarak izleyin.</span><span class="sxs-lookup"><span data-stu-id="c7374-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="c7374-213">gRPC ölçümlerini gözlemlemenin bir diğer yolu da Application Insights'ın [Microsoft.ApplicationInsights.EventCounterCollector paketini](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)kullanarak karşı veri yakalamaktır.</span><span class="sxs-lookup"><span data-stu-id="c7374-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="c7374-214">Kurulumdan sonra Application Insights çalışma zamanında ortak .NET sayaçlarını toplar.</span><span class="sxs-lookup"><span data-stu-id="c7374-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="c7374-215">gRPC sayaçları varsayılan olarak toplanmaz, ancak App Insights [ek sayaçları içerecek şekilde özelleştirilebilir.](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)</span><span class="sxs-lookup"><span data-stu-id="c7374-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="c7374-216">*Startup.cs*toplamak için Uygulama Öngörüsü için gRPC sayaçlarını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c7374-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c7374-217">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c7374-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
