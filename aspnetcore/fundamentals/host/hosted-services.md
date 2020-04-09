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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="7f935-103">ASP.NET Core'da barındırılan hizmetlerle arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="7f935-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="7f935-104">Yazar: [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="7f935-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f935-105">ASP.NET Core'da arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="7f935-106">Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7f935-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="7f935-107">Bu konu üç barındırılan hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f935-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="7f935-108">Zamanlayıcıüzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="7f935-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="7f935-109">Kapsamlı hizmeti etkinleştiren [barındırılan hizmet.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="7f935-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7f935-110">Kapsamlı hizmet bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7f935-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="7f935-111">Sırayla çalışan sıralı arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="7f935-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="7f935-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f935-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="7f935-113">İşçi Hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="7f935-113">Worker Service template</span></span>

<span data-ttu-id="7f935-114">ASP.NET Çekirdek İşçi Hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f935-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="7f935-115">İşçi Hizmeti şablonundan oluşturulan bir uygulama, İşçi SDK'yı proje dosyasında belirtir:</span><span class="sxs-lookup"><span data-stu-id="7f935-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="7f935-116">Barındırılan bir hizmet uygulaması için şablonu temel olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="7f935-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="7f935-117">Paket</span><span class="sxs-lookup"><span data-stu-id="7f935-117">Package</span></span>

<span data-ttu-id="7f935-118">İşçi Hizmeti şablonuna dayalı `Microsoft.NET.Sdk.Worker` bir uygulama SDK kullanır ve [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine açık bir paket başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="7f935-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="7f935-119">Örneğin, örnek uygulamanın proje dosyasına bakın (*BackgroundTasksSample.csproj*).</span><span class="sxs-lookup"><span data-stu-id="7f935-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="7f935-120">`Microsoft.NET.Sdk.Web` SDK kullanan web uygulamaları için [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketi paylaşılan çerçeveden dolaylı olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="7f935-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="7f935-121">Uygulamanın proje dosyasında açık bir paket başvurusu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7f935-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="7f935-122">IHostedService arayüzü</span><span class="sxs-lookup"><span data-stu-id="7f935-122">IHostedService interface</span></span>

<span data-ttu-id="7f935-123">Arabirim, <xref:Microsoft.Extensions.Hosting.IHostedService> ana bilgisayar tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7f935-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="7f935-124">[StartAsync(İptalToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` arka plan görevini başlatmak için mantık içerir.</span><span class="sxs-lookup"><span data-stu-id="7f935-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="7f935-125">`StartAsync`*önce*denir :</span><span class="sxs-lookup"><span data-stu-id="7f935-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="7f935-126">Uygulamanın istek işleme ardışık hattı`Startup.Configure`yapılandırılır ( ).</span><span class="sxs-lookup"><span data-stu-id="7f935-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="7f935-127">Sunucu başlatıldı ve [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7f935-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="7f935-128">Varsayılan davranış, barındırılan hizmetin uygulamanın ardışık alanı yapılandırıldıktan `StartAsync` sonra `ApplicationStarted` çalışır ve çağrılacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="7f935-129">Varsayılan davranışı değiştirmek için, (aşağıdaki`VideosWatcher` örnekte) aşağıdaki `ConfigureWebHostDefaults`çağrıdan sonra barındırılan hizmeti ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7f935-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="7f935-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar zarif bir kapatma gerçekleştirirken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7f935-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="7f935-131">`StopAsync`arka plan görevini sona erdirme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7f935-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="7f935-132">Yönetilmeyen kaynakları elden çıkarmak için uygulayın <xref:System.IDisposable> ve [sonlandırıcılar (yıkıcılar).](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="7f935-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="7f935-133">İptal belirteci, kapatma işleminin artık zarif olmaması gerektiğini belirtmek için varsayılan beş saniyelik zaman aşımına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7f935-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="7f935-134">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="7f935-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="7f935-135">Uygulamanın gerçekleştirdiği kalan arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="7f935-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="7f935-136">Çağrılan tüm `StopAsync` yöntemler derhal geri dönmelidir.</span><span class="sxs-lookup"><span data-stu-id="7f935-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="7f935-137">Ancak, iptal istendikten&mdash;sonra görevler terk edilmez, arayan tüm görevleri tamamlamak için bekler.</span><span class="sxs-lookup"><span data-stu-id="7f935-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="7f935-138">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın `StopAsync` işlemi başarısız olursa), çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="7f935-139">Bu nedenle, çağrılan herhangi `StopAsync` bir yöntem veya yürütülen işlemler oluşmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="7f935-140">Varsayılan beş ikinci kapatma süresini uzatmak için ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7f935-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="7f935-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel Ana Bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="7f935-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="7f935-142">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="7f935-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="7f935-143">Web Host'u kullanırken zaman ekme yapılandırma ayarını kapatın.</span><span class="sxs-lookup"><span data-stu-id="7f935-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="7f935-144">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="7f935-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="7f935-145">Barındırılan hizmet, uygulama nın başlatılmasında bir kez etkinleştirilir ve uygulama kapatmada zarif bir şekilde kapatılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="7f935-146">Arka plan görev yürütme sırasında `Dispose` bir hata atılırsa, çağrılmasa `StopAsync` bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f935-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="7f935-147">BackgroundService taban sınıfı</span><span class="sxs-lookup"><span data-stu-id="7f935-147">BackgroundService base class</span></span>

<span data-ttu-id="7f935-148"><xref:Microsoft.Extensions.Hosting.BackgroundService>uzun süren <xref:Microsoft.Extensions.Hosting.IHostedService>bir uygulama için bir taban sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7f935-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="7f935-149">[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) arka plan hizmetini çalıştırmak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="7f935-150">Uygulama, arka <xref:System.Threading.Tasks.Task> plan hizmetinin tüm ömrünü temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="7f935-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="7f935-151">[ExecuteAsync asynchronous hale gelinceye](https://github.com/dotnet/extensions/issues/2149)kadar başka hizmet `await`başlatılır , örneğin çağırarak .</span><span class="sxs-lookup"><span data-stu-id="7f935-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="7f935-152">`ExecuteAsync`'de uzun, engelleme başlatma çalışması yapmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7f935-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="7f935-153">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) ev sahibi blokları `ExecuteAsync` tamamlamak için bekliyor.</span><span class="sxs-lookup"><span data-stu-id="7f935-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="7f935-154">İptal belirteci, [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) çağrıldığında tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7f935-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="7f935-155">Hizmeti zarif `ExecuteAsync` bir şekilde kapatmak için iptal jetonu ateşlendiğinde uygulamanız derhal bitmelidir.</span><span class="sxs-lookup"><span data-stu-id="7f935-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="7f935-156">Aksi takdirde, hizmet kapatma zaman anında zarafetle kapanır.</span><span class="sxs-lookup"><span data-stu-id="7f935-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="7f935-157">Daha fazla bilgi için [IHostedService arabirimi](#ihostedservice-interface) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7f935-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="7f935-158">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="7f935-158">Timed background tasks</span></span>

<span data-ttu-id="7f935-159">Zamanlanmış bir arka plan görevi [System.Threading.Timer](xref:System.Threading.Timer) sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f935-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="7f935-160">Zamanlayıcı görevin `DoWork` yöntemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="7f935-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="7f935-161">Zamanlayıcı, servis `StopAsync` konteyneri üzerine atıldığında devre dışı `Dispose`bırakılır ve bertaraf edilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="7f935-162">Önceki <xref:System.Threading.Timer> yürütmelerin `DoWork` tamamlanmasını beklemez, bu nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="7f935-163">[Interlocked.Increment,](xref:System.Threading.Interlocked.Increment*) birden çok iş parçacığının aynı anda güncellenmemesini `executionCount` sağlayan bir atomik işlem olarak yürütme sayacını artım için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="7f935-164">Hizmet `IHostBuilder.ConfigureServices` (*Program.cs)* `AddHostedService` uzantısı yöntemi ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="7f935-165">Arka plan görevinde kapsamlı bir hizmeti tüketme</span><span class="sxs-lookup"><span data-stu-id="7f935-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="7f935-166">[Bir BackgroundService](#backgroundservice-base-class)içinde [kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) kullanmak için bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7f935-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="7f935-167">Barındırılan bir hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="7f935-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="7f935-168">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7f935-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="7f935-169">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="7f935-169">In the following example:</span></span>

* <span data-ttu-id="7f935-170">Hizmet asynchronous olduğunu.</span><span class="sxs-lookup"><span data-stu-id="7f935-170">The service is asynchronous.</span></span> <span data-ttu-id="7f935-171">Yöntem `DoWork` bir `Task`.</span><span class="sxs-lookup"><span data-stu-id="7f935-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="7f935-172">Gösteri amacıyla, `DoWork` yöntemde on saniyelik bir gecikme bekleniyor.</span><span class="sxs-lookup"><span data-stu-id="7f935-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="7f935-173">Servise bir enjekte <xref:Microsoft.Extensions.Logging.ILogger> edilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="7f935-174">Barındırılan hizmet, `DoWork` yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözmek için bir kapsam oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7f935-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="7f935-175">`DoWork`bir `Task`, içinde beklenen `ExecuteAsync`döner:</span><span class="sxs-lookup"><span data-stu-id="7f935-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="7f935-176">Hizmetler `IHostBuilder.ConfigureServices` *(Program.cs)* kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="7f935-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="7f935-177">Barındırılan hizmet uzantı yöntemine `AddHostedService` kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="7f935-178">Sıraya alıbdaki arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="7f935-178">Queued background tasks</span></span>

<span data-ttu-id="7f935-179">Arka plan görev sırası .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> 'e dayanır[(geçici olarak ASP.NET Core için yerleşik olarak zamanlanır):](https://github.com/aspnet/Hosting/issues/1280)</span><span class="sxs-lookup"><span data-stu-id="7f935-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="7f935-180">Aşağıdaki `QueueHostedService` örnekte:</span><span class="sxs-lookup"><span data-stu-id="7f935-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="7f935-181">Yöntem, `BackgroundProcessing` beklenen `Task`bir , `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="7f935-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="7f935-182">Kuyruktaki arka plan görevleri sıradan `BackgroundProcessing`silinip yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f935-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="7f935-183">Hizmet durmadan önce çalışma öğeleri `StopAsync`bekleniyor.</span><span class="sxs-lookup"><span data-stu-id="7f935-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="7f935-184">Bir `MonitorLoop` hizmet, `w` anahtar bir giriş aygıtında seçildiğinde barındırılan hizmet için sıralama görevlerini işler:</span><span class="sxs-lookup"><span data-stu-id="7f935-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="7f935-185">Servise `IBackgroundTaskQueue` `MonitorLoop` enjekte edilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="7f935-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem`bir iş öğesini enqueue için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="7f935-187">Çalışma öğesi uzun süren bir arka plan görevi benzetimi:</span><span class="sxs-lookup"><span data-stu-id="7f935-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="7f935-188">Üç 5 saniyelik gecikmeler`Task.Delay`yürütülür ( ).</span><span class="sxs-lookup"><span data-stu-id="7f935-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="7f935-189">Görev `try-catch` iptal <xref:System.OperationCanceledException> edilirse deyim yakalar.</span><span class="sxs-lookup"><span data-stu-id="7f935-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="7f935-190">Hizmetler `IHostBuilder.ConfigureServices` *(Program.cs)* kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="7f935-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="7f935-191">Barındırılan hizmet uzantı yöntemine `AddHostedService` kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="7f935-192">`MontiorLoop`olarak `Program.Main`başlatılır:</span><span class="sxs-lookup"><span data-stu-id="7f935-192">`MontiorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f935-193">ASP.NET Core'da arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="7f935-194">Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7f935-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="7f935-195">Bu konu üç barındırılan hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f935-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="7f935-196">Zamanlayıcıüzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="7f935-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="7f935-197">Kapsamlı hizmeti etkinleştiren [barındırılan hizmet.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="7f935-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7f935-198">Kapsamlı hizmet bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kullanabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="7f935-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="7f935-199">Sırayla çalışan sıralı arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="7f935-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="7f935-200">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f935-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="7f935-201">Paket</span><span class="sxs-lookup"><span data-stu-id="7f935-201">Package</span></span>

<span data-ttu-id="7f935-202">[Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun veya [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7f935-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="7f935-203">IHostedService arayüzü</span><span class="sxs-lookup"><span data-stu-id="7f935-203">IHostedService interface</span></span>

<span data-ttu-id="7f935-204">Barındırılan <xref:Microsoft.Extensions.Hosting.IHostedService> hizmetler arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="7f935-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="7f935-205">Arabirim, ana bilgisayar tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7f935-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="7f935-206">[StartAsync(İptalToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` arka plan görevini başlatmak için mantık içerir.</span><span class="sxs-lookup"><span data-stu-id="7f935-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="7f935-207">[Web Host'u](xref:fundamentals/host/web-host) `StartAsync` kullanırken, sunucu başladıktan ve [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) başlatıldıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="7f935-208">[Genel Ana Bilgisayar](xref:fundamentals/host/generic-host) `StartAsync` kullanırken, `ApplicationStarted` tetiklenmeden önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="7f935-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar zarif bir kapatma gerçekleştirirken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7f935-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="7f935-210">`StopAsync`arka plan görevini sona erdirme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7f935-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="7f935-211">Yönetilmeyen kaynakları elden çıkarmak için uygulayın <xref:System.IDisposable> ve [sonlandırıcılar (yıkıcılar).](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="7f935-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="7f935-212">İptal belirteci, kapatma işleminin artık zarif olmaması gerektiğini belirtmek için varsayılan beş saniyelik zaman aşımına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7f935-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="7f935-213">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="7f935-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="7f935-214">Uygulamanın gerçekleştirdiği kalan arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="7f935-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="7f935-215">Çağrılan tüm `StopAsync` yöntemler derhal geri dönmelidir.</span><span class="sxs-lookup"><span data-stu-id="7f935-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="7f935-216">Ancak, iptal istendikten&mdash;sonra görevler terk edilmez, arayan tüm görevleri tamamlamak için bekler.</span><span class="sxs-lookup"><span data-stu-id="7f935-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="7f935-217">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın `StopAsync` işlemi başarısız olursa), çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="7f935-218">Bu nedenle, çağrılan herhangi `StopAsync` bir yöntem veya yürütülen işlemler oluşmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="7f935-219">Varsayılan beş ikinci kapatma süresini uzatmak için ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7f935-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="7f935-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel Ana Bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="7f935-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="7f935-221">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="7f935-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="7f935-222">Web Host'u kullanırken zaman ekme yapılandırma ayarını kapatın.</span><span class="sxs-lookup"><span data-stu-id="7f935-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="7f935-223">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="7f935-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="7f935-224">Barındırılan hizmet, uygulama nın başlatılmasında bir kez etkinleştirilir ve uygulama kapatmada zarif bir şekilde kapatılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="7f935-225">Arka plan görev yürütme sırasında `Dispose` bir hata atılırsa, çağrılmasa `StopAsync` bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f935-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="7f935-226">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="7f935-226">Timed background tasks</span></span>

<span data-ttu-id="7f935-227">Zamanlanmış bir arka plan görevi [System.Threading.Timer](xref:System.Threading.Timer) sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f935-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="7f935-228">Zamanlayıcı görevin `DoWork` yöntemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="7f935-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="7f935-229">Zamanlayıcı, servis `StopAsync` konteyneri üzerine atıldığında devre dışı `Dispose`bırakılır ve bertaraf edilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="7f935-230">Önceki <xref:System.Threading.Timer> yürütmelerin `DoWork` tamamlanmasını beklemez, bu nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7f935-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="7f935-231">Hizmet uzantı `Startup.ConfigureServices` yöntemine `AddHostedService` kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="7f935-232">Arka plan görevinde kapsamlı bir hizmeti tüketme</span><span class="sxs-lookup"><span data-stu-id="7f935-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="7f935-233">[Kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) bir `IHostedService`kapsam içinde kullanmak için bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7f935-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="7f935-234">Barındırılan bir hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="7f935-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="7f935-235">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7f935-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="7f935-236">Aşağıdaki örnekte, <xref:Microsoft.Extensions.Logging.ILogger> hizmete bir enjekte edilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="7f935-237">Barındırılan hizmet, `DoWork` yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözümlemek için bir kapsam oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7f935-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="7f935-238">Hizmetler `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7f935-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7f935-239">Uygulama `IHostedService` uzantı yöntemine `AddHostedService` kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="7f935-240">Sıraya alıbdaki arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="7f935-240">Queued background tasks</span></span>

<span data-ttu-id="7f935-241">Arka plan görev sırası .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> 'e[(geçici olarak ASP.NET Çekirdek için dahil edilmesi zamanlanır)](https://github.com/aspnet/Hosting/issues/1280)dayanır:</span><span class="sxs-lookup"><span data-stu-id="7f935-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="7f935-242">Sırada, `QueueHostedService`arka plan görevleri dequeued ve uzun çalışan `IHostedService`uygulamak için bir taban sınıf olan bir [BackgroundService](#backgroundservice-base-class)olarak yürütülür:</span><span class="sxs-lookup"><span data-stu-id="7f935-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="7f935-243">Hizmetler `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7f935-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7f935-244">Uygulama `IHostedService` uzantı yöntemine `AddHostedService` kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7f935-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="7f935-245">Dizin sayfası modeli sınıfında:</span><span class="sxs-lookup"><span data-stu-id="7f935-245">In the Index page model class:</span></span>

* <span data-ttu-id="7f935-246">Yapıcıiçine `IBackgroundTaskQueue` enjekte edilir ve `Queue`atanır.</span><span class="sxs-lookup"><span data-stu-id="7f935-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="7f935-247">Bir <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> enjekte edilir ve `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="7f935-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="7f935-248">Fabrika, bir kapsam içinde <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>hizmet oluşturmak için kullanılan , örnekleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f935-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="7f935-249">Uygulamanın `AppDbContext` [(kapsamlı bir hizmeti)](xref:fundamentals/dependency-injection#service-lifetimes)veritabanı kayıtlarını `IBackgroundTaskQueue` (singleton hizmeti) yazmak için kullanmak için bir kapsam oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7f935-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7f935-250">Dizin sayfasında **Görev Ekle** düğmesi seçildiğinde, `OnPostAddTask` yöntem yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f935-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="7f935-251">`QueueBackgroundWorkItem`bir iş öğesini sıraya almak için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="7f935-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7f935-252">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7f935-252">Additional resources</span></span>

* [<span data-ttu-id="7f935-253">IHostedService ve BackgroundService sınıfı ile mikro hizmetlerde arka plan görevlerini uygulayın</span><span class="sxs-lookup"><span data-stu-id="7f935-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="7f935-254">Azure Uygulama Hizmetinde Web İşleri ile arka plan görevlerini çalıştırma</span><span class="sxs-lookup"><span data-stu-id="7f935-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
