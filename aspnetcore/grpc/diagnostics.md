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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET'te gRPC'de günlük ve tanılama

Yazar: [James Newton-King](https://twitter.com/jamesnk)

Bu makalede, sorunları gidermeye yardımcı olmak için bir gRPC uygulamasından tanılama toplamak için kılavuz sağlar. Ele alınan konular:

* **Günlük** - [.NET Core günlük](xref:fundamentals/logging/index)lerine yazılmış yapılandırılmış günlükler. <xref:Microsoft.Extensions.Logging.ILogger>günlükleri yazmak için uygulama çerçeveleri tarafından ve kullanıcılar tarafından bir uygulamada kendi oturum açma için kullanılır.
* **İzleme** - Kullanılarak `DiaganosticSource` yazılan bir işlemle ilgili olaylar ve `Activity`. Tanı kaynağından gelen izler genellikle [Uygulama Öngörüleri](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ve [OpenTelemetri](https://github.com/open-telemetry/opentelemetry-dotnet)gibi kitaplıklar tarafından uygulama telemetrisini toplamak için kullanılır.
* Ölçümler - Veri ölçülerinin zaman aralıkları, örneğin, saniyedeki **istekler** üzerinden gösterimi. Ölçümler kullanılarak `EventCounter` yayılır ve [dotnet sayaçları](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) komut satırı aracı kullanılarak veya [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)ile gözlemlenebilir.

## <a name="logging"></a>Günlüğe kaydetme

gRPC hizmetleri ve gRPC istemcisi [.NET Core günlük](xref:fundamentals/logging/index)lerini kullanarak günlükleri yazmak. Günlükler, uygulamalarınızda beklenmeyen davranışları hata ayıklamanız gerektiğinde başlamak için iyi bir yerdir.

### <a name="grpc-services-logging"></a>gRPC hizmetleri günlüğü

> [!WARNING]
> Sunucu tarafındaki günlükler uygulamanızdan hassas bilgiler içerebilir. **Üretim** uygulamalarından GitHub gibi genel forumlara asla ham günlükler göndermeyin.

gRPC hizmetleri ASP.NET Core'da barındırılan bu yana, ASP.NET Core günlük sistemini kullanır. Varsayılan yapılandırmada, gRPC çok az bilgi kaydeder, ancak bu yapılandırılabilir. ASP.NET Core [günlük](xref:fundamentals/logging/index#configuration) yapılandırma sı hakkında ayrıntılı bilgi için ASP.NET Core günlük üzerindeki belgelere bakın.

gRPC kategorisi altında `Grpc` günlükleri ekler. gRPC'den ayrıntılı günlükleri etkinleştirmek `Grpc` için, önekleri *appsettings.json* dosyanızdaki `Debug` düzeye doğru, `LogLevel` aşağıdaki öğeleri `Logging`aşağıdaki alt bölüme ekleyerek yapılandırın:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Ayrıca bu *Startup.cs* ile `ConfigureLogging`yapılandırabilirsiniz:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

JSON tabanlı yapılandırma kullanmıyorsanız, yapılandırma sisteminizde aşağıdaki yapılandırma değerini ayarlayın:

* `Logging:LogLevel:Grpc` = `Debug`

İç içe geçme yapılandırma değerlerini nasıl belirtin belirlemek için yapılandırma sisteminizin belgelerini denetleyin. Örneğin, ortam değişkenleri kullanılırken, `_` `:` (örneğin,) `Logging__LogLevel__Grpc`yerine iki karakter kullanılır.

Uygulamanız için `Debug` daha ayrıntılı tanılama lar toplarken düzeyi kullanmanızı öneririz. Düzey `Trace` çok düşük düzeyde tanılama üretir ve uygulamanızdaki sorunları tanılamak için nadiren gereklidir.

#### <a name="sample-logging-output"></a>Örnek günlük çıktısı

Aşağıda, bir gRPC `Debug` hizmeti düzeyinde konsol çıkışına bir örnek verilmiştir:

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

### <a name="access-server-side-logs"></a>Sunucu tarafındaki günlüklere erişin

Sunucu tarafındaki günlüklere nasıl eriştüğün, içinde çalıştığınız ortama bağlıdır.

#### <a name="as-a-console-app"></a>Konsol uygulaması olarak

Bir konsol uygulamasında çalışıyorsanız, [Konsol kaydedicisi](xref:fundamentals/logging/index#console-provider) varsayılan olarak etkinleştirilmelidir. konsolda gRPC günlükleri görünür.

#### <a name="other-environments"></a>Diğer ortamlar

Uygulama başka bir ortama (örneğin, Docker, Kubernetes veya Windows <xref:fundamentals/logging/index> Service) dağıtılırsa, ortama uygun günlük sağlayıcılarını nasıl yapılandırılabilirsiniz hakkında daha fazla bilgi için bkz.

### <a name="grpc-client-logging"></a>gRPC istemci günlüğü

> [!WARNING]
> İstemci tarafı günlükleri uygulamanızdan hassas bilgiler içerebilir. **Üretim** uygulamalarından GitHub gibi genel forumlara asla ham günlükler göndermeyin.

.NET istemcisinden günlük almak için, istemcinin `GrpcChannelOptions.LoggerFactory` kanalı oluşturulduğunda özelliği ayarlayabilirsiniz. Bir ASP.NET Core uygulamasından gRPC hizmetini arıyorsanız, logger fabrikası bağımlılık enjeksiyonundan (DI) çözülebilir:

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

İstemci günlüğe kaydetmeyi etkinleştirmek için alternatif bir yol istemci oluşturmak için [gRPC istemci fabrikasını](xref:grpc/clientfactory) kullanmaktır. İstemci fabrikasına kayıtlı ve DI'den çözümlenen bir gRPC istemcisi, uygulamanın yapılandırılmış günlüğe kaydetmesini otomatik olarak kullanır.

Uygulamanız DI kullanmıyorsa `ILoggerFactory` [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)ile yeni bir örnek oluşturabilirsiniz. Bu yönteme erişmek için uygulamanıza [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) paketini ekleyin.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC istemci günlük kapsamları

gRPC istemcisi, gRPC araması sırasında yapılan günlüklere günlük [kapsamı](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) ekler. Kapsam gRPC çağrısı ile ilgili meta verilere sahiptir:

* **GrpcMethodType** - gRPC yöntem türü. Olası değerler enum adlarıdır, `Grpc.Core.MethodType` örneğin Unary
* **GrpcUri** - gRPC yönteminin göreceli URI, örneğin / selamlıyorum. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Örnek günlük çıktısı

Aşağıda, bir gRPC `Debug` istemcisi düzeyinde konsol çıktısı örneği verilmiştir:

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

## <a name="tracing"></a>İzleme

gRPC hizmetleri ve gRPC istemcisi [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) ve [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)kullanarak gRPC aramaları hakkında bilgi sağlar.

* .NET gRPC, gRPC çağrısını temsil etmek için bir etkinlik kullanır.
* İzleme olayları gRPC arama etkinliğinin başında ve durağında tanı kaynağına yazılır.
* İzleme, gRPC akış çağrılarının kullanım ömrü boyunca iletilerin ne zaman gönderildiği hakkında bilgi yakalamaz.

### <a name="grpc-service-tracing"></a>gRPC hizmet izleme

gRPC hizmetleri, gelen HTTP istekleriyle ilgili olayları bildiren ASP.NET Core'da barındırılır. ASP.NET Core'un sağladığı mevcut HTTP isteği tanılamalarına gRPC'ye özgü meta veriler eklenir.

* Tanılama kaynak `Microsoft.AspNetCore`adıdır.
* Etkinlik adı `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * gRPC çağrısı tarafından çağrılan gRPC yönteminin adı, ad `grpc.method`ile etiket olarak eklenir.
  * Tamamlandığında gRPC çağrısının durum kodu adı `grpc.status_code`ile bir etiket olarak eklenir.

### <a name="grpc-client-tracing"></a>gRPC istemci izleme

.NET gRPC istemcisi gRPC aramaları yapmak için kullanır. `HttpClient` Tanılama olayları yazsa da, `HttpClient` .NET gRPC istemcisi özel bir tanılama kaynağı, etkinlik ve olaylar sağlar, böylece bir gRPC çağrısı hakkında tam bilgi toplanabilir.

* Tanılama kaynak `Grpc.Net.Client`adıdır.
* Etkinlik adı `Grpc.Net.Client.GrpcOut`.
  * gRPC çağrısı tarafından çağrılan gRPC yönteminin adı, ad `grpc.method`ile etiket olarak eklenir.
  * Tamamlandığında gRPC çağrısının durum kodu adı `grpc.status_code`ile bir etiket olarak eklenir.

### <a name="collecting-tracing"></a>İzleme yi toplama

En kolay kullanım `DiagnosticSource` yolu, [uygulamanızda Uygulama Öngörüleri](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) veya [OpenTelemetri](https://github.com/open-telemetry/opentelemetry-dotnet) gibi bir telemetri kitaplığını yapılandırmaktır. Kitaplık, diğer uygulama telemetrisi ile birlikte gRPC çağrıları hakkındaki bilgileri işleyecek.

İzleme, Uygulama Öngörüleri gibi yönetilen bir hizmette görüntülenebilir veya kendi dağıtılmış izleme sisteminizi çalıştırmayı seçebilirsiniz. OpenTelemetry [Jaeger](https://www.jaegertracing.io/) ve [Zipkin](https://zipkin.io/)veri izleme dışa aktarAn destekler.

`DiagnosticSource`kullanarak `DiagnosticListener`koddaki olayları izleme tüketebilir. Kodlu bir tanı kaynağını dinleme hakkında bilgi için [DiagnosticSource kullanıcı kılavuzuna](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)bakın.

> [!NOTE]
> Telemetri kitaplıkları şu anda gRPC'ye özgü `Grpc.Net.Client.GrpcOut` telemetriyi yakalayamıyor. Bu izleme yakalama telemetri kütüphaneleri geliştirmek için çalışmalar devam etmektedir.

## <a name="metrics"></a>Ölçümler

Ölçümler, örneğin saniyedeki istekler gibi zaman aralıklarına göre veri ölçülerinin bir temsilidir. Ölçümler verileri, bir uygulamanın durumunun üst düzeyde gözlemlemesine olanak tanır. .NET gRPC ölçümleri kullanılarak `EventCounter`yayılır.

### <a name="grpc-service-metrics"></a>gRPC hizmet ölçümleri

gRPC sunucu ölçümleri `Grpc.AspNetCore.Server` olay kaynağında raporlanır.

| Adı                      | Açıklama                   |
| --------------------------|-------------------------------|
| `total-calls`             | Toplam Aramalar                   |
| `current-calls`           | Güncel Aramalar                 |
| `calls-failed`            | Toplam Aramalar Başarısız Oldu            |
| `calls-deadline-exceeded` | Toplam Arama Süresi Aşıldı |
| `messages-sent`           | Gönderilen Toplam İleti Sayısı           |
| `messages-received`       | Alınan Toplam İleti       |
| `calls-unimplemented`     | Uygulanmamış Toplam Aramalar     |

ASP.NET Core da olay kaynağı `Microsoft.AspNetCore.Hosting` üzerinde kendi ölçümleri sağlar.

### <a name="grpc-client-metrics"></a>gRPC istemci ölçümleri

gRPC istemci ölçümleri `Grpc.Net.Client` olay kaynağında raporlanır.

| Adı                      | Açıklama                   |
| --------------------------|-------------------------------|
| `total-calls`             | Toplam Aramalar                   |
| `current-calls`           | Güncel Aramalar                 |
| `calls-failed`            | Toplam Aramalar Başarısız Oldu            |
| `calls-deadline-exceeded` | Toplam Arama Süresi Aşıldı |
| `messages-sent`           | Gönderilen Toplam İleti Sayısı           |
| `messages-received`       | Alınan Toplam İleti       |

### <a name="observe-metrics"></a>Ölçümleri gözlemle

[dotnet sayaçları,](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) özel sistem durumu izleme ve birinci düzey performans araştırması için bir performans izleme aracıdır. Bir .NET uygulamasını `Grpc.AspNetCore.Server` sağlayıcı `Grpc.Net.Client` adı olarak veya sağlayıcı adı olarak izleyin.

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

gRPC ölçümlerini gözlemlemenin bir diğer yolu da Application Insights'ın [Microsoft.ApplicationInsights.EventCounterCollector paketini](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)kullanarak karşı veri yakalamaktır. Kurulumdan sonra Application Insights çalışma zamanında ortak .NET sayaçlarını toplar. gRPC sayaçları varsayılan olarak toplanmaz, ancak App Insights [ek sayaçları içerecek şekilde özelleştirilebilir.](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)

*Startup.cs*toplamak için Uygulama Öngörüsü için gRPC sayaçlarını belirtin:

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

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
