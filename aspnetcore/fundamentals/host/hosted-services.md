---
title: ASP.NET Core'da barındırılan hizmetlerle arka plan görevleri
author: rick-anderson
description: ASP.NET Core'da barındırılan hizmetlerle arka plan görevlerini nasıl uygulayacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666203"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>ASP.NET Core'da barındırılan hizmetlerle arka plan görevleri

Yazar: [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core'da arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir. Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır. Bu konu üç barındırılan hizmet örneği sağlar:

* Zamanlayıcıüzerinde çalışan arka plan görevi.
* Kapsamlı hizmeti etkinleştiren [barındırılan hizmet.](xref:fundamentals/dependency-injection#service-lifetimes) Kapsamlı hizmet bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection)kullanabilirsiniz.
* Sırayla çalışan sıralı arka plan görevleri.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>İşçi Hizmeti şablonu

ASP.NET Çekirdek İşçi Hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar. İşçi Hizmeti şablonundan oluşturulan bir uygulama, İşçi SDK'yı proje dosyasında belirtir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Barındırılan bir hizmet uygulaması için şablonu temel olarak kullanmak için:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Paket

İşçi Hizmeti şablonuna dayalı `Microsoft.NET.Sdk.Worker` bir uygulama SDK kullanır ve [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine açık bir paket başvurusu vardır. Örneğin, örnek uygulamanın proje dosyasına bakın (*BackgroundTasksSample.csproj*).

`Microsoft.NET.Sdk.Web` SDK kullanan web uygulamaları için [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketi paylaşılan çerçeveden dolaylı olarak başvurulur. Uygulamanın proje dosyasında açık bir paket başvurusu gerekmez.

## <a name="ihostedservice-interface"></a>IHostedService arayüzü

Arabirim, <xref:Microsoft.Extensions.Hosting.IHostedService> ana bilgisayar tarafından yönetilen nesneler için iki yöntem tanımlar:

* [StartAsync(İptalToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` arka plan görevini başlatmak için mantık içerir. `StartAsync`*önce*denir :

  * Uygulamanın istek işleme ardışık hattı`Startup.Configure`yapılandırılır ( ).
  * Sunucu başlatıldı ve [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.

  Varsayılan davranış, barındırılan hizmetin uygulamanın ardışık alanı yapılandırıldıktan `StartAsync` sonra `ApplicationStarted` çalışır ve çağrılacak şekilde değiştirilebilir. Varsayılan davranışı değiştirmek için, (aşağıdaki`VideosWatcher` örnekte) aşağıdaki `ConfigureWebHostDefaults`çağrıdan sonra barındırılan hizmeti ekleyin:

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar zarif bir kapatma gerçekleştirirken tetiklenir. `StopAsync`arka plan görevini sona erdirme mantığını içerir. Yönetilmeyen kaynakları elden çıkarmak için uygulayın <xref:System.IDisposable> ve [sonlandırıcılar (yıkıcılar).](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

  İptal belirteci, kapatma işleminin artık zarif olmaması gerektiğini belirtmek için varsayılan beş saniyelik zaman aşımına sahiptir. Belirteç üzerinde iptal istendiğinde:

  * Uygulamanın gerçekleştirdiği kalan arka plan işlemleri iptal edilmelidir.
  * Çağrılan tüm `StopAsync` yöntemler derhal geri dönmelidir.

  Ancak, iptal istendikten&mdash;sonra görevler terk edilmez, arayan tüm görevleri tamamlamak için bekler.

  Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın `StopAsync` işlemi başarısız olursa), çağrılmayabilir. Bu nedenle, çağrılan herhangi `StopAsync` bir yöntem veya yürütülen işlemler oluşmayabilir.

  Varsayılan beş ikinci kapatma süresini uzatmak için ayarlayın:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel Ana Bilgisayar kullanırken. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Web Host'u kullanırken zaman ekme yapılandırma ayarını kapatın. Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.

Barındırılan hizmet, uygulama nın başlatılmasında bir kez etkinleştirilir ve uygulama kapatmada zarif bir şekilde kapatılır. Arka plan görev yürütme sırasında `Dispose` bir hata atılırsa, çağrılmasa `StopAsync` bile çağrılmalıdır.

## <a name="backgroundservice-base-class"></a>BackgroundService taban sınıfı

<xref:Microsoft.Extensions.Hosting.BackgroundService>uzun süren <xref:Microsoft.Extensions.Hosting.IHostedService>bir uygulama için bir taban sınıftır.

[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) arka plan hizmetini çalıştırmak için çağrılır. Uygulama, arka <xref:System.Threading.Tasks.Task> plan hizmetinin tüm ömrünü temsil eden bir döndürür. [ExecuteAsync asynchronous hale gelinceye](https://github.com/dotnet/extensions/issues/2149)kadar başka hizmet `await`başlatılır , örneğin çağırarak . `ExecuteAsync`'de uzun, engelleme başlatma çalışması yapmaktan kaçının. [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) ev sahibi blokları `ExecuteAsync` tamamlamak için bekliyor.

İptal belirteci, [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) çağrıldığında tetiklenir. Hizmeti zarif `ExecuteAsync` bir şekilde kapatmak için iptal jetonu ateşlendiğinde uygulamanız derhal bitmelidir. Aksi takdirde, hizmet kapatma zaman anında zarafetle kapanır. Daha fazla bilgi için [IHostedService arabirimi](#ihostedservice-interface) bölümüne bakın.

## <a name="timed-background-tasks"></a>Zamanlanmış arka plan görevleri

Zamanlanmış bir arka plan görevi [System.Threading.Timer](xref:System.Threading.Timer) sınıfını kullanır. Zamanlayıcı görevin `DoWork` yöntemini tetikler. Zamanlayıcı, servis `StopAsync` konteyneri üzerine atıldığında devre dışı `Dispose`bırakılır ve bertaraf edilir:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Önceki <xref:System.Threading.Timer> yürütmelerin `DoWork` tamamlanmasını beklemez, bu nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir. [Interlocked.Increment,](xref:System.Threading.Interlocked.Increment*) birden çok iş parçacığının aynı anda güncellenmemesini `executionCount` sağlayan bir atomik işlem olarak yürütme sayacını artım için kullanılır.

Hizmet `IHostBuilder.ConfigureServices` (*Program.cs)* `AddHostedService` uzantısı yöntemi ile kaydedilir:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Arka plan görevinde kapsamlı bir hizmeti tüketme

[Bir BackgroundService](#backgroundservice-base-class)içinde [kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) kullanmak için bir kapsam oluşturun. Barındırılan bir hizmet için varsayılan olarak kapsam oluşturulmaz.

Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir. Aşağıdaki örnekte:

* Hizmet asynchronous olduğunu. Yöntem `DoWork` bir `Task`. Gösteri amacıyla, `DoWork` yöntemde on saniyelik bir gecikme bekleniyor.
* Servise bir enjekte <xref:Microsoft.Extensions.Logging.ILogger> edilir.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Barındırılan hizmet, `DoWork` yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözmek için bir kapsam oluşturur. `DoWork`bir `Task`, içinde beklenen `ExecuteAsync`döner:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Hizmetler `IHostBuilder.ConfigureServices` *(Program.cs)* kayıtlıdır. Barındırılan hizmet uzantı yöntemine `AddHostedService` kaydedilir:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Sıraya alıbdaki arka plan görevleri

Arka plan görev sırası .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> 'e dayanır[(geçici olarak ASP.NET Core için yerleşik olarak zamanlanır):](https://github.com/aspnet/Hosting/issues/1280)

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Aşağıdaki `QueueHostedService` örnekte:

* Yöntem, `BackgroundProcessing` beklenen `Task`bir , `ExecuteAsync`.
* Kuyruktaki arka plan görevleri sıradan `BackgroundProcessing`silinip yürütülür.
* Hizmet durmadan önce çalışma öğeleri `StopAsync`bekleniyor.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Bir `MonitorLoop` hizmet, `w` anahtar bir giriş aygıtında seçildiğinde barındırılan hizmet için sıralama görevlerini işler:

* Servise `IBackgroundTaskQueue` `MonitorLoop` enjekte edilir.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`bir iş öğesini enqueue için çağrılır.
* Çalışma öğesi uzun süren bir arka plan görevi benzetimi:
  * Üç 5 saniyelik gecikmeler`Task.Delay`yürütülür ( ).
  * Görev `try-catch` iptal <xref:System.OperationCanceledException> edilirse deyim yakalar.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Hizmetler `IHostBuilder.ConfigureServices` *(Program.cs)* kayıtlıdır. Barındırılan hizmet uzantı yöntemine `AddHostedService` kaydedilir:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop`olarak `Program.Main`başlatılır:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core'da arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir. Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır. Bu konu üç barındırılan hizmet örneği sağlar:

* Zamanlayıcıüzerinde çalışan arka plan görevi.
* Kapsamlı hizmeti etkinleştiren [barındırılan hizmet.](xref:fundamentals/dependency-injection#service-lifetimes) Kapsamlı hizmet bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kullanabilirsiniz
* Sırayla çalışan sıralı arka plan görevleri.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="package"></a>Paket

[Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun veya [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine bir paket başvurusu ekleyin.

## <a name="ihostedservice-interface"></a>IHostedService arayüzü

Barındırılan <xref:Microsoft.Extensions.Hosting.IHostedService> hizmetler arabirimi uygular. Arabirim, ana bilgisayar tarafından yönetilen nesneler için iki yöntem tanımlar:

* [StartAsync(İptalToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` arka plan görevini başlatmak için mantık içerir. [Web Host'u](xref:fundamentals/host/web-host) `StartAsync` kullanırken, sunucu başladıktan ve [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) başlatıldıktan sonra çağrılır. [Genel Ana Bilgisayar](xref:fundamentals/host/generic-host) `StartAsync` kullanırken, `ApplicationStarted` tetiklenmeden önce çağrılır.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar zarif bir kapatma gerçekleştirirken tetiklenir. `StopAsync`arka plan görevini sona erdirme mantığını içerir. Yönetilmeyen kaynakları elden çıkarmak için uygulayın <xref:System.IDisposable> ve [sonlandırıcılar (yıkıcılar).](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

  İptal belirteci, kapatma işleminin artık zarif olmaması gerektiğini belirtmek için varsayılan beş saniyelik zaman aşımına sahiptir. Belirteç üzerinde iptal istendiğinde:

  * Uygulamanın gerçekleştirdiği kalan arka plan işlemleri iptal edilmelidir.
  * Çağrılan tüm `StopAsync` yöntemler derhal geri dönmelidir.

  Ancak, iptal istendikten&mdash;sonra görevler terk edilmez, arayan tüm görevleri tamamlamak için bekler.

  Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın `StopAsync` işlemi başarısız olursa), çağrılmayabilir. Bu nedenle, çağrılan herhangi `StopAsync` bir yöntem veya yürütülen işlemler oluşmayabilir.

  Varsayılan beş ikinci kapatma süresini uzatmak için ayarlayın:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel Ana Bilgisayar kullanırken. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Web Host'u kullanırken zaman ekme yapılandırma ayarını kapatın. Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.

Barındırılan hizmet, uygulama nın başlatılmasında bir kez etkinleştirilir ve uygulama kapatmada zarif bir şekilde kapatılır. Arka plan görev yürütme sırasında `Dispose` bir hata atılırsa, çağrılmasa `StopAsync` bile çağrılmalıdır.

## <a name="timed-background-tasks"></a>Zamanlanmış arka plan görevleri

Zamanlanmış bir arka plan görevi [System.Threading.Timer](xref:System.Threading.Timer) sınıfını kullanır. Zamanlayıcı görevin `DoWork` yöntemini tetikler. Zamanlayıcı, servis `StopAsync` konteyneri üzerine atıldığında devre dışı `Dispose`bırakılır ve bertaraf edilir:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Önceki <xref:System.Threading.Timer> yürütmelerin `DoWork` tamamlanmasını beklemez, bu nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.

Hizmet uzantı `Startup.ConfigureServices` yöntemine `AddHostedService` kaydedilir:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Arka plan görevinde kapsamlı bir hizmeti tüketme

[Kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) bir `IHostedService`kapsam içinde kullanmak için bir kapsam oluşturun. Barındırılan bir hizmet için varsayılan olarak kapsam oluşturulmaz.

Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir. Aşağıdaki örnekte, <xref:Microsoft.Extensions.Logging.ILogger> hizmete bir enjekte edilir:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Barındırılan hizmet, `DoWork` yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözümlemek için bir kapsam oluşturur:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Hizmetler `Startup.ConfigureServices`. Uygulama `IHostedService` uzantı yöntemine `AddHostedService` kaydedilir:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Sıraya alıbdaki arka plan görevleri

Arka plan görev sırası .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> 'e[(geçici olarak ASP.NET Çekirdek için dahil edilmesi zamanlanır)](https://github.com/aspnet/Hosting/issues/1280)dayanır:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Sırada, `QueueHostedService`arka plan görevleri dequeued ve uzun çalışan `IHostedService`uygulamak için bir taban sınıf olan bir [BackgroundService](#backgroundservice-base-class)olarak yürütülür:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Hizmetler `Startup.ConfigureServices`. Uygulama `IHostedService` uzantı yöntemine `AddHostedService` kaydedilir:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Dizin sayfası modeli sınıfında:

* Yapıcıiçine `IBackgroundTaskQueue` enjekte edilir ve `Queue`atanır.
* Bir <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> enjekte edilir ve `_serviceScopeFactory`. Fabrika, bir kapsam içinde <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>hizmet oluşturmak için kullanılan , örnekleri oluşturmak için kullanılır. Uygulamanın `AppDbContext` [(kapsamlı bir hizmeti)](xref:fundamentals/dependency-injection#service-lifetimes)veritabanı kayıtlarını `IBackgroundTaskQueue` (singleton hizmeti) yazmak için kullanmak için bir kapsam oluşturulur.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Dizin sayfasında **Görev Ekle** düğmesi seçildiğinde, `OnPostAddTask` yöntem yürütülür. `QueueBackgroundWorkItem`bir iş öğesini sıraya almak için çağrılır:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [IHostedService ve BackgroundService sınıfı ile mikro hizmetlerde arka plan görevlerini uygulayın](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Azure Uygulama Hizmetinde Web İşleri ile arka plan görevlerini çalıştırma](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
