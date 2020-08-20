---
title: ASP.NET Core içinde barındırılan hizmetlerle arka plan görevleri
author: rick-anderson
description: ASP.NET Core içinde barındırılan hizmetlerle arka plan görevlerinin nasıl uygulanacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 04841eb4f6adfec76020d3fe61601037c3fc0733
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635352"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="a8d22-103">ASP.NET Core içinde barındırılan hizmetlerle arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a8d22-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="a8d22-104">, [Jeow li Hua](https://github.com/huan086) tarafından</span><span class="sxs-lookup"><span data-stu-id="a8d22-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8d22-105">ASP.NET Core, arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a8d22-106">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="a8d22-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a8d22-107">Bu konuda üç barındırılan hizmet örneği sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="a8d22-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a8d22-108">Bir Zamanlayıcı üzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="a8d22-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a8d22-109">[Kapsamlı bir hizmeti](xref:fundamentals/dependency-injection#service-lifetimes)etkinleştiren barındırılan hizmet.</span><span class="sxs-lookup"><span data-stu-id="a8d22-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a8d22-110">Kapsamlı hizmet [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="a8d22-111">Sıralı olarak çalışan sıraya alınmış arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="a8d22-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a8d22-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8d22-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="a8d22-113">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="a8d22-113">Worker Service template</span></span>

<span data-ttu-id="a8d22-114">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="a8d22-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="a8d22-115">Çalışan hizmeti şablonundan oluşturulan bir uygulama, çalışan SDK 'sını proje dosyasında belirtir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="a8d22-116">Şablonu bir barındırılan hizmetler uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="a8d22-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="a8d22-117">Paket</span><span class="sxs-lookup"><span data-stu-id="a8d22-117">Package</span></span>

<span data-ttu-id="a8d22-118">Çalışan hizmeti şablonunu temel alan bir uygulama `Microsoft.NET.Sdk.Worker` SDK kullanır ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine açık bir paket başvurusu içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="a8d22-119">Örneğin, örnek uygulamanın proje dosyasına (*Backgroundtaskssample. csproj*) bakın.</span><span class="sxs-lookup"><span data-stu-id="a8d22-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="a8d22-120">SDK kullanan Web uygulamaları için `Microsoft.NET.Sdk.Web` , [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine, paylaşılan çerçeveden dolaylı olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="a8d22-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="a8d22-121">Uygulamanın proje dosyasındaki açık bir paket başvurusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a8d22-122">Ihostedservice arabirimi</span><span class="sxs-lookup"><span data-stu-id="a8d22-122">IHostedService interface</span></span>

<span data-ttu-id="a8d22-123"><xref:Microsoft.Extensions.Hosting.IHostedService>Arabirim, konak tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="a8d22-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a8d22-124">[Startasync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` arka plan görevinin başlatılacağı mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a8d22-125">`StartAsync`Şu kadar *çağrılır:*</span><span class="sxs-lookup"><span data-stu-id="a8d22-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="a8d22-126">Uygulamanın istek işleme işlem hattı yapılandırıldı ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="a8d22-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="a8d22-127">Sunucu başlatıldı ve [ıapplicationlifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="a8d22-128">Varsayılan davranış, barındırılan hizmetin `StartAsync` , uygulamanın işlem hattı yapılandırıldıktan ve çağrıldıktan sonra çalışması için değiştirilebilir `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="a8d22-129">Varsayılan davranışı değiştirmek için, öğesini çağırdıktan sonra barındırılan hizmeti ( `VideosWatcher` Aşağıdaki örnekte) ekleyin `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="a8d22-130">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): konak düzgün kapanma yaparken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a8d22-131">`StopAsync` arka plan görevinin bitiş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a8d22-132"><xref:System.IDisposable>Yönetilmeyen kaynakların atılmaya yönelik uygulama ve [sonlandırıcılar (Yıkıcılar)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="a8d22-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a8d22-133">İptal belirtecinin, kapanma işleminin artık düzgün şekilde olmaması gerektiğini göstermek için varsayılan beş saniyelik bir zaman aşımı vardır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a8d22-134">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="a8d22-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a8d22-135">Uygulamanın gerçekleştirdiği diğer arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a8d22-136">İçinde çağrılan tüm yöntemler `StopAsync` hemen döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a8d22-137">Ancak, bir iptal işlemi istendikten sonra görevler terk edilmez &mdash; ve bu, çağıran tüm görevlerin tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="a8d22-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a8d22-138">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın işlemi başarısız olur), `StopAsync` çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a8d22-139">Bu nedenle, veya üzerinde gerçekleştirilen işlemleri çağıran Yöntemler `StopAsync` gerçekleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a8d22-140">Varsayılan beş saniyelik kapatılma zaman aşımını uzatmak için, şunu ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="a8d22-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a8d22-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Genel ana bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="a8d22-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a8d22-142">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a8d22-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a8d22-143">Web ana bilgisayarı kullanılırken, kapatılma zaman aşımı konak yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="a8d22-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a8d22-144">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a8d22-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a8d22-145">Barındırılan hizmet, uygulama başlangıcında bir kez etkinleştirilir ve uygulama kapatılırken düzgün şekilde kapanır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a8d22-146">Arka plan görevinin yürütülmesi sırasında bir hata oluşursa, `Dispose` `StopAsync` çağrılmadıysa bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="a8d22-147">BackgroundService temel sınıfı</span><span class="sxs-lookup"><span data-stu-id="a8d22-147">BackgroundService base class</span></span>

<span data-ttu-id="a8d22-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> , uzun süre çalışan uygulamaya yönelik bir temel sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="a8d22-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="a8d22-149">Arka plan hizmetini çalıştırmak için [ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) çağırılır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="a8d22-150">Uygulama, <xref:System.Threading.Tasks.Task> arka plan hizmetinin tüm ömrünü temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="a8d22-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="a8d22-151">ExecuteAsync, çağırarak gibi [zaman uyumsuz hale](https://github.com/dotnet/extensions/issues/2149)gelene kadar başka bir hizmet başlatılamaz `await` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="a8d22-152">İçinde başlatma işini uzun süre gerçekleştirmekten kaçının `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="a8d22-153">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) içindeki konak blokları tamamlanmasını bekliyor `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="a8d22-154">[Ihostedservice. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) çağrıldığında iptal belirteci tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="a8d22-155">`ExecuteAsync`Hizmeti sorunsuz bir şekilde kapatmak için iptal belirteci tetiklendiğinde uygulamanızın uygulamanız hemen sona ermesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="a8d22-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="a8d22-156">Aksi takdirde, hizmet, kapanmanın zaman aşımından sonra kapanmadığını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="a8d22-157">Daha fazla bilgi için [ıhostedservice arabirimi](#ihostedservice-interface) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="a8d22-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a8d22-158">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a8d22-158">Timed background tasks</span></span>

<span data-ttu-id="a8d22-159">Zamanlanmış bir arka plan görevi, [System. Threading. Timer](xref:System.Threading.Timer) sınıfından kullanımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="a8d22-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a8d22-160">Zamanlayıcı, görevin `DoWork` metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="a8d22-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a8d22-161">Zamanlayıcı devre dışı bırakılır `StopAsync` ve hizmet kapsayıcısı bırakıldığında bırakıldı `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="a8d22-162">, <xref:System.Threading.Timer> Önceki yürütmelerin bitmesini beklemez, bu `DoWork` nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="a8d22-163">[Interkilitlendi. Increment](xref:System.Threading.Interlocked.Increment*) , yürütme sayacını bir atomik işlem olarak artırmak için kullanılır, bu da birden çok iş parçacığının eşzamanlı olarak güncelleştirilmesini sağlar `executionCount` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="a8d22-164">Hizmet, `IHostBuilder.ConfigureServices` (*program.cs*) `AddHostedService` öğesine uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a8d22-165">Bir arka plan görevinde kapsamlı bir hizmeti kullanma</span><span class="sxs-lookup"><span data-stu-id="a8d22-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a8d22-166">Bir [backgroundservice](#backgroundservice-base-class)içinde [kapsamlı hizmetler](xref:fundamentals/dependency-injection#service-lifetimes) kullanmak için bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a8d22-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="a8d22-167">Barındırılan hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="a8d22-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a8d22-168">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a8d22-169">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="a8d22-169">In the following example:</span></span>

* <span data-ttu-id="a8d22-170">Hizmet zaman uyumsuz.</span><span class="sxs-lookup"><span data-stu-id="a8d22-170">The service is asynchronous.</span></span> <span data-ttu-id="a8d22-171">`DoWork`Yöntemi bir döndürür `Task` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="a8d22-172">Tanıtım amacıyla, yöntemde on saniyelik bir gecikme beklenir `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="a8d22-173"><xref:Microsoft.Extensions.Logging.ILogger>Hizmete eklenen bir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a8d22-174">Barındırılan hizmet, yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözümlemek üzere bir kapsam oluşturur `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="a8d22-175">`DoWork` şunu döndürür `Task` `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="a8d22-176">Hizmetler ' de kaydedilir `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a8d22-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a8d22-177">Barındırılan hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a8d22-178">Sıraya alınan arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a8d22-178">Queued background tasks</span></span>

<span data-ttu-id="a8d22-179">Arka plan görev kuyruğu, .NET 4. x 'i temel alır <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :</span><span class="sxs-lookup"><span data-stu-id="a8d22-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a8d22-180">Aşağıdaki `QueueHostedService` örnekte:</span><span class="sxs-lookup"><span data-stu-id="a8d22-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="a8d22-181">`BackgroundProcessing`Yöntemi `Task` , ' de beklemiş bir döndürür `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="a8d22-182">Kuyruktaki arka plan görevleri, içinde kuyruklanmış ve yürütülür `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="a8d22-183">İş öğeleri, hizmetin durdurulmadan önce bekletildi `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="a8d22-184">Bir `MonitorLoop` hizmet, giriş cihazında anahtar her seçildiğinde barındırılan hizmet için görevleri sıraya alır `w` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="a8d22-185">, `IBackgroundTaskQueue` `MonitorLoop` Hizmete eklenir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="a8d22-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` bir iş öğesini kuyruğa almak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="a8d22-187">Çalışma öğesi uzun süre çalışan bir arka plan görevinin benzetimini yapar:</span><span class="sxs-lookup"><span data-stu-id="a8d22-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="a8d22-188">Üç 5-ikinci gecikme () yürütülür `Task.Delay` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="a8d22-189">`try-catch` <xref:System.OperationCanceledException> Görev iptal edildiğinde bir ifade yakalar.</span><span class="sxs-lookup"><span data-stu-id="a8d22-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="a8d22-190">Hizmetler ' de kaydedilir `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a8d22-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a8d22-191">Barındırılan hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="a8d22-192">`MonitorLoop` Başlangıç `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-192">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8d22-193">ASP.NET Core, arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a8d22-194">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="a8d22-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a8d22-195">Bu konuda üç barındırılan hizmet örneği sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="a8d22-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a8d22-196">Bir Zamanlayıcı üzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="a8d22-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a8d22-197">[Kapsamlı bir hizmeti](xref:fundamentals/dependency-injection#service-lifetimes)etkinleştiren barındırılan hizmet.</span><span class="sxs-lookup"><span data-stu-id="a8d22-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a8d22-198">Kapsamlı hizmet [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kullanabilir</span><span class="sxs-lookup"><span data-stu-id="a8d22-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="a8d22-199">Sıralı olarak çalışan sıraya alınmış arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="a8d22-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a8d22-200">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8d22-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="a8d22-201">Paket</span><span class="sxs-lookup"><span data-stu-id="a8d22-201">Package</span></span>

<span data-ttu-id="a8d22-202">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a8d22-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a8d22-203">Ihostedservice arabirimi</span><span class="sxs-lookup"><span data-stu-id="a8d22-203">IHostedService interface</span></span>

<span data-ttu-id="a8d22-204">Barındırılan hizmetler, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="a8d22-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a8d22-205">Arabirim, konak tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="a8d22-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a8d22-206">[Startasync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` arka plan görevinin başlatılacağı mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a8d22-207">[Web konağını](xref:fundamentals/host/web-host)kullanırken `StartAsync` sunucu başlatıldıktan ve [ıapplicationlifetime değerinden sonra çağrılır. applicationstarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="a8d22-208">[Genel ana bilgisayar](xref:fundamentals/host/generic-host)kullanılırken, `StartAsync` tetiklendikten önce çağrılır `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="a8d22-209">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): konak düzgün kapanma yaparken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a8d22-210">`StopAsync` arka plan görevinin bitiş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a8d22-211"><xref:System.IDisposable>Yönetilmeyen kaynakların atılmaya yönelik uygulama ve [sonlandırıcılar (Yıkıcılar)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="a8d22-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a8d22-212">İptal belirtecinin, kapanma işleminin artık düzgün şekilde olmaması gerektiğini göstermek için varsayılan beş saniyelik bir zaman aşımı vardır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a8d22-213">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="a8d22-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a8d22-214">Uygulamanın gerçekleştirdiği diğer arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a8d22-215">İçinde çağrılan tüm yöntemler `StopAsync` hemen döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a8d22-216">Ancak, bir iptal işlemi istendikten sonra görevler terk edilmez &mdash; ve bu, çağıran tüm görevlerin tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="a8d22-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a8d22-217">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın işlemi başarısız olur), `StopAsync` çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a8d22-218">Bu nedenle, veya üzerinde gerçekleştirilen işlemleri çağıran Yöntemler `StopAsync` gerçekleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a8d22-219">Varsayılan beş saniyelik kapatılma zaman aşımını uzatmak için, şunu ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="a8d22-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a8d22-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Genel ana bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="a8d22-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a8d22-221">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a8d22-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a8d22-222">Web ana bilgisayarı kullanılırken, kapatılma zaman aşımı konak yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="a8d22-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a8d22-223">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a8d22-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a8d22-224">Barındırılan hizmet, uygulama başlangıcında bir kez etkinleştirilir ve uygulama kapatılırken düzgün şekilde kapanır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a8d22-225">Arka plan görevinin yürütülmesi sırasında bir hata oluşursa, `Dispose` `StopAsync` çağrılmadıysa bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a8d22-226">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a8d22-226">Timed background tasks</span></span>

<span data-ttu-id="a8d22-227">Zamanlanmış bir arka plan görevi, [System. Threading. Timer](xref:System.Threading.Timer) sınıfından kullanımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="a8d22-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a8d22-228">Zamanlayıcı, görevin `DoWork` metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="a8d22-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a8d22-229">Zamanlayıcı devre dışı bırakılır `StopAsync` ve hizmet kapsayıcısı bırakıldığında bırakıldı `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="a8d22-230">, <xref:System.Threading.Timer> Önceki yürütmelerin bitmesini beklemez, bu `DoWork` nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="a8d22-231">Hizmet, `Startup.ConfigureServices` `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a8d22-232">Bir arka plan görevinde kapsamlı bir hizmeti kullanma</span><span class="sxs-lookup"><span data-stu-id="a8d22-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a8d22-233">[Kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) bir içinde kullanmak için `IHostedService` bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a8d22-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="a8d22-234">Barındırılan hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="a8d22-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a8d22-235">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a8d22-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a8d22-236">Aşağıdaki örnekte, <xref:Microsoft.Extensions.Logging.ILogger> hizmetine bir hizmet eklenmiş olur:</span><span class="sxs-lookup"><span data-stu-id="a8d22-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a8d22-237">Barındırılan hizmet, kendi metodunu çağırmak için kapsamlı arka plan görev hizmetini çözümlemek üzere bir kapsam oluşturur `DoWork` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="a8d22-238">Hizmetler ' de kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8d22-239">`IHostedService`Uygulama, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a8d22-240">Sıraya alınan arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a8d22-240">Queued background tasks</span></span>

<span data-ttu-id="a8d22-241">Arka plan görev kuyruğu .NET Framework 4. x ' i temel alır <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([ASP.NET Core için yerleşik olarak zamanlanmış olarak zamanlandı](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="a8d22-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a8d22-242">' De `QueueHostedService` , kuyruktaki arka plan görevleri, uzun süre çalışan uygulama için temel bir sınıf olan bir [backgroundservice](#backgroundservice-base-class)olarak kuyruğa alınır ve yürütülür `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="a8d22-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="a8d22-243">Hizmetler ' de kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8d22-244">`IHostedService`Uygulama, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8d22-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="a8d22-245">Dizin sayfası model sınıfında:</span><span class="sxs-lookup"><span data-stu-id="a8d22-245">In the Index page model class:</span></span>

* <span data-ttu-id="a8d22-246">`IBackgroundTaskQueue`Oluşturucuya eklenir ve öğesine atanır `Queue` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="a8d22-247"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Eklenen ve öğesine atandı `_serviceScopeFactory` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="a8d22-248">Fabrika, <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsam içinde hizmet oluşturmak için kullanılan örnekleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a8d22-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="a8d22-249">Uygulama (bir tekil `AppDbContext` hizmet) içinde veritabanı kayıtları yazmak için uygulamanın ( [kapsamlı bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes)) kullanılabilmesi için bir kapsam oluşturulur `IBackgroundTaskQueue` .</span><span class="sxs-lookup"><span data-stu-id="a8d22-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="a8d22-250">Dizin sayfasında **Görev Ekle** düğmesi seçildiğinde `OnPostAddTask` Yöntem yürütülür.</span><span class="sxs-lookup"><span data-stu-id="a8d22-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="a8d22-251">`QueueBackgroundWorkItem` bir iş öğesini kuyruğa almak için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="a8d22-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a8d22-252">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a8d22-252">Additional resources</span></span>

* [<span data-ttu-id="a8d22-253">Ihostedservice ve BackgroundService sınıfıyla mikro hizmetlerde arka plan görevleri uygulama</span><span class="sxs-lookup"><span data-stu-id="a8d22-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="a8d22-254">Azure App Service Web Işleri ile arka plan görevleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a8d22-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
