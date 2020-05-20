---
<span data-ttu-id="e4146-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e4146-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e4146-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e4146-102">'Blazor'</span></span>
- <span data-ttu-id="e4146-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e4146-103">'Identity'</span></span>
- <span data-ttu-id="e4146-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e4146-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e4146-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e4146-105">'Razor'</span></span>
- <span data-ttu-id="e4146-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e4146-106">'SignalR' uid:</span></span> 

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="e4146-107">ASP.NET Core içinde barındırılan hizmetlerle arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="e4146-107">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="e4146-108">, [Jeow li Hua](https://github.com/huan086) tarafından</span><span class="sxs-lookup"><span data-stu-id="e4146-108">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e4146-109">ASP.NET Core, arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-109">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="e4146-110">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="e4146-110">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e4146-111">Bu konuda üç barındırılan hizmet örneği sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="e4146-111">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="e4146-112">Bir Zamanlayıcı üzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="e4146-112">Background task that runs on a timer.</span></span>
* <span data-ttu-id="e4146-113">[Kapsamlı bir hizmeti](xref:fundamentals/dependency-injection#service-lifetimes)etkinleştiren barındırılan hizmet.</span><span class="sxs-lookup"><span data-stu-id="e4146-113">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="e4146-114">Kapsamlı hizmet [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-114">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="e4146-115">Sıralı olarak çalışan sıraya alınmış arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="e4146-115">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="e4146-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4146-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="e4146-117">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="e4146-117">Worker Service template</span></span>

<span data-ttu-id="e4146-118">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="e4146-118">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="e4146-119">Çalışan hizmeti şablonundan oluşturulan bir uygulama, çalışan SDK 'sını proje dosyasında belirtir:</span><span class="sxs-lookup"><span data-stu-id="e4146-119">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="e4146-120">Şablonu bir barındırılan hizmetler uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="e4146-120">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="e4146-121">Paket</span><span class="sxs-lookup"><span data-stu-id="e4146-121">Package</span></span>

<span data-ttu-id="e4146-122">Çalışan hizmeti şablonunu temel alan bir uygulama `Microsoft.NET.Sdk.Worker` SDK kullanır ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine açık bir paket başvurusu içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-122">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="e4146-123">Örneğin, örnek uygulamanın proje dosyasına (*Backgroundtaskssample. csproj*) bakın.</span><span class="sxs-lookup"><span data-stu-id="e4146-123">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="e4146-124">SDK kullanan Web uygulamaları için `Microsoft.NET.Sdk.Web` , [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine, paylaşılan çerçeveden dolaylı olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="e4146-124">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="e4146-125">Uygulamanın proje dosyasındaki açık bir paket başvurusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e4146-125">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="e4146-126">Ihostedservice arabirimi</span><span class="sxs-lookup"><span data-stu-id="e4146-126">IHostedService interface</span></span>

<span data-ttu-id="e4146-127"><xref:Microsoft.Extensions.Hosting.IHostedService>Arabirim, konak tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="e4146-127">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="e4146-128">[Startasync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync`arka plan görevinin başlatılacağı mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="e4146-129">`StartAsync`Şu kadar *çağrılır:*</span><span class="sxs-lookup"><span data-stu-id="e4146-129">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="e4146-130">Uygulamanın istek işleme işlem hattı yapılandırıldı ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="e4146-130">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="e4146-131">Sunucu başlatıldı ve [ıapplicationlifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e4146-131">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="e4146-132">Varsayılan davranış, barındırılan hizmetin `StartAsync` , uygulamanın işlem hattı yapılandırıldıktan ve çağrıldıktan sonra çalışması için değiştirilebilir `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="e4146-132">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="e4146-133">Varsayılan davranışı değiştirmek için, öğesini çağırdıktan sonra barındırılan hizmeti ( `VideosWatcher` Aşağıdaki örnekte) ekleyin `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="e4146-133">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="e4146-134">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar düzgün bir şekilde kapanma gerçekleştirirken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e4146-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="e4146-135">`StopAsync`arka plan görevinin bitiş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-135">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="e4146-136"><xref:System.IDisposable>Yönetilmeyen kaynakların atılmaya yönelik uygulama ve [sonlandırıcılar (Yıkıcılar)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="e4146-136">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="e4146-137">İptal belirtecinin, kapanma işleminin artık düzgün şekilde olmaması gerektiğini göstermek için varsayılan beş saniyelik bir zaman aşımı vardır.</span><span class="sxs-lookup"><span data-stu-id="e4146-137">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="e4146-138">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="e4146-138">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="e4146-139">Uygulamanın gerçekleştirdiği diğer arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e4146-139">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="e4146-140">İçinde çağrılan tüm yöntemler `StopAsync` hemen döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="e4146-140">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="e4146-141">Ancak, bir iptal işlemi istendikten sonra görevler terk edilmez &mdash; ve bu, çağıran tüm görevlerin tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="e4146-141">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="e4146-142">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın işlemi başarısız olur), `StopAsync` çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-142">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="e4146-143">Bu nedenle, veya üzerinde gerçekleştirilen işlemleri çağıran Yöntemler `StopAsync` gerçekleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-143">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="e4146-144">Varsayılan beş saniyelik kapatılma zaman aşımını uzatmak için, şunu ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="e4146-144">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="e4146-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel ana bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="e4146-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="e4146-146">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e4146-146">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="e4146-147">Web ana bilgisayarı kullanılırken, kapatılma zaman aşımı konak yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="e4146-147">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="e4146-148">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e4146-148">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="e4146-149">Barındırılan hizmet, uygulama başlangıcında bir kez etkinleştirilir ve uygulama kapatılırken düzgün şekilde kapanır.</span><span class="sxs-lookup"><span data-stu-id="e4146-149">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="e4146-150">Arka plan görevinin yürütülmesi sırasında bir hata oluşursa, `Dispose` `StopAsync` çağrılmadıysa bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e4146-150">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="e4146-151">BackgroundService temel sınıfı</span><span class="sxs-lookup"><span data-stu-id="e4146-151">BackgroundService base class</span></span>

<span data-ttu-id="e4146-152"><xref:Microsoft.Extensions.Hosting.BackgroundService>, uzun süre çalışan uygulamaya yönelik bir temel sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="e4146-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="e4146-153">Arka plan hizmetini çalıştırmak için [ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) çağırılır.</span><span class="sxs-lookup"><span data-stu-id="e4146-153">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="e4146-154">Uygulama, <xref:System.Threading.Tasks.Task> arka plan hizmetinin tüm ömrünü temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e4146-154">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="e4146-155">ExecuteAsync, çağırarak gibi [zaman uyumsuz hale](https://github.com/dotnet/extensions/issues/2149)gelene kadar başka bir hizmet başlatılamaz `await` .</span><span class="sxs-lookup"><span data-stu-id="e4146-155">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="e4146-156">İçinde başlatma işini uzun süre gerçekleştirmekten kaçının `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="e4146-156">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="e4146-157">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) içindeki konak blokları tamamlanmasını bekliyor `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="e4146-157">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="e4146-158">[Ihostedservice. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) çağrıldığında iptal belirteci tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e4146-158">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="e4146-159">`ExecuteAsync`Hizmeti sorunsuz bir şekilde kapatmak için iptal belirteci tetiklendiğinde uygulamanızın uygulamanız hemen sona ermesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="e4146-159">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="e4146-160">Aksi takdirde, hizmet, kapanmanın zaman aşımından sonra kapanmadığını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e4146-160">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="e4146-161">Daha fazla bilgi için [ıhostedservice arabirimi](#ihostedservice-interface) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e4146-161">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="e4146-162">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="e4146-162">Timed background tasks</span></span>

<span data-ttu-id="e4146-163">Zamanlanmış bir arka plan görevi, [System. Threading. Timer](xref:System.Threading.Timer) sınıfından kullanımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="e4146-163">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="e4146-164">Zamanlayıcı, görevin `DoWork` metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="e4146-164">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="e4146-165">Zamanlayıcı devre dışı bırakılır `StopAsync` ve hizmet kapsayıcısı bırakıldığında bırakıldı `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="e4146-165">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="e4146-166">, <xref:System.Threading.Timer> Önceki yürütmelerin bitmesini beklemez, bu `DoWork` nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-166">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="e4146-167">[Interkilitlendi. Increment](xref:System.Threading.Interlocked.Increment*) , yürütme sayacını bir atomik işlem olarak artırmak için kullanılır, bu da birden çok iş parçacığının eşzamanlı olarak güncelleştirilmesini sağlar `executionCount` .</span><span class="sxs-lookup"><span data-stu-id="e4146-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="e4146-168">Hizmet, `IHostBuilder.ConfigureServices` (*program.cs*) `AddHostedService` öğesine uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e4146-168">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="e4146-169">Bir arka plan görevinde kapsamlı bir hizmeti kullanma</span><span class="sxs-lookup"><span data-stu-id="e4146-169">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="e4146-170">Bir [backgroundservice](#backgroundservice-base-class)içinde [kapsamlı hizmetler](xref:fundamentals/dependency-injection#service-lifetimes) kullanmak için bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e4146-170">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="e4146-171">Barındırılan hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="e4146-171">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="e4146-172">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-172">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="e4146-173">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="e4146-173">In the following example:</span></span>

* <span data-ttu-id="e4146-174">Hizmet zaman uyumsuz.</span><span class="sxs-lookup"><span data-stu-id="e4146-174">The service is asynchronous.</span></span> <span data-ttu-id="e4146-175">`DoWork`Yöntemi bir döndürür `Task` .</span><span class="sxs-lookup"><span data-stu-id="e4146-175">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="e4146-176">Tanıtım amacıyla, yöntemde on saniyelik bir gecikme beklenir `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="e4146-176">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="e4146-177"><xref:Microsoft.Extensions.Logging.ILogger>Hizmete eklenen bir.</span><span class="sxs-lookup"><span data-stu-id="e4146-177">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="e4146-178">Barındırılan hizmet, yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözümlemek üzere bir kapsam oluşturur `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="e4146-178">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="e4146-179">`DoWork`şunu döndürür `Task` `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="e4146-179">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="e4146-180">Hizmetler ' de kaydedilir `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="e4146-180">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="e4146-181">Barındırılan hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e4146-181">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="e4146-182">Sıraya alınan arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="e4146-182">Queued background tasks</span></span>

<span data-ttu-id="e4146-183">Arka plan görev kuyruğu, .NET 4. x 'i temel alır <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([ASP.NET Core için isteğe](https://github.com/aspnet/Hosting/issues/1280)bağlı olarak zamanlanmış olarak zamanlandı):</span><span class="sxs-lookup"><span data-stu-id="e4146-183">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="e4146-184">Aşağıdaki `QueueHostedService` örnekte:</span><span class="sxs-lookup"><span data-stu-id="e4146-184">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="e4146-185">`BackgroundProcessing`Yöntemi `Task` , ' de beklemiş bir döndürür `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="e4146-185">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="e4146-186">Kuyruktaki arka plan görevleri, içinde kuyruklanmış ve yürütülür `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="e4146-186">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="e4146-187">İş öğeleri, hizmetin durdurulmadan önce bekletildi `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="e4146-187">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="e4146-188">Bir `MonitorLoop` hizmet, giriş cihazında anahtar her seçildiğinde barındırılan hizmet için görevleri sıraya alır `w` :</span><span class="sxs-lookup"><span data-stu-id="e4146-188">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="e4146-189">, `IBackgroundTaskQueue` `MonitorLoop` Hizmete eklenir.</span><span class="sxs-lookup"><span data-stu-id="e4146-189">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="e4146-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem`bir iş öğesini kuyruğa almak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e4146-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="e4146-191">Çalışma öğesi uzun süre çalışan bir arka plan görevinin benzetimini yapar:</span><span class="sxs-lookup"><span data-stu-id="e4146-191">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="e4146-192">Üç 5-ikinci gecikme () yürütülür `Task.Delay` .</span><span class="sxs-lookup"><span data-stu-id="e4146-192">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="e4146-193">`try-catch` <xref:System.OperationCanceledException> Görev iptal edildiğinde bir ifade yakalar.</span><span class="sxs-lookup"><span data-stu-id="e4146-193">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="e4146-194">Hizmetler ' de kaydedilir `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="e4146-194">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="e4146-195">Barındırılan hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e4146-195">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="e4146-196">`MonitorLoop`Başlangıç `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="e4146-196">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e4146-197">ASP.NET Core, arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-197">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="e4146-198">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="e4146-198">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e4146-199">Bu konuda üç barındırılan hizmet örneği sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="e4146-199">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="e4146-200">Bir Zamanlayıcı üzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="e4146-200">Background task that runs on a timer.</span></span>
* <span data-ttu-id="e4146-201">[Kapsamlı bir hizmeti](xref:fundamentals/dependency-injection#service-lifetimes)etkinleştiren barındırılan hizmet.</span><span class="sxs-lookup"><span data-stu-id="e4146-201">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="e4146-202">Kapsamlı hizmet [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kullanabilir</span><span class="sxs-lookup"><span data-stu-id="e4146-202">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="e4146-203">Sıralı olarak çalışan sıraya alınmış arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="e4146-203">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="e4146-204">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4146-204">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="e4146-205">Paket</span><span class="sxs-lookup"><span data-stu-id="e4146-205">Package</span></span>

<span data-ttu-id="e4146-206">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e4146-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="e4146-207">Ihostedservice arabirimi</span><span class="sxs-lookup"><span data-stu-id="e4146-207">IHostedService interface</span></span>

<span data-ttu-id="e4146-208">Barındırılan hizmetler, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="e4146-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e4146-209">Arabirim, konak tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="e4146-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="e4146-210">[Startasync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync`arka plan görevinin başlatılacağı mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="e4146-211">[Web konağını](xref:fundamentals/host/web-host)kullanırken `StartAsync` sunucu başlatıldıktan ve [ıapplicationlifetime değerinden sonra çağrılır. applicationstarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e4146-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="e4146-212">[Genel ana bilgisayar](xref:fundamentals/host/generic-host)kullanılırken, `StartAsync` tetiklendikten önce çağrılır `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="e4146-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="e4146-213">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar düzgün bir şekilde kapanma gerçekleştirirken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e4146-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="e4146-214">`StopAsync`arka plan görevinin bitiş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="e4146-215"><xref:System.IDisposable>Yönetilmeyen kaynakların atılmaya yönelik uygulama ve [sonlandırıcılar (Yıkıcılar)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="e4146-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="e4146-216">İptal belirtecinin, kapanma işleminin artık düzgün şekilde olmaması gerektiğini göstermek için varsayılan beş saniyelik bir zaman aşımı vardır.</span><span class="sxs-lookup"><span data-stu-id="e4146-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="e4146-217">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="e4146-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="e4146-218">Uygulamanın gerçekleştirdiği diğer arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e4146-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="e4146-219">İçinde çağrılan tüm yöntemler `StopAsync` hemen döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="e4146-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="e4146-220">Ancak, bir iptal işlemi istendikten sonra görevler terk edilmez &mdash; ve bu, çağıran tüm görevlerin tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="e4146-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="e4146-221">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın işlemi başarısız olur), `StopAsync` çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="e4146-222">Bu nedenle, veya üzerinde gerçekleştirilen işlemleri çağıran Yöntemler `StopAsync` gerçekleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="e4146-223">Varsayılan beş saniyelik kapatılma zaman aşımını uzatmak için, şunu ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="e4146-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="e4146-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel ana bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="e4146-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="e4146-225">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e4146-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="e4146-226">Web ana bilgisayarı kullanılırken, kapatılma zaman aşımı konak yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="e4146-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="e4146-227">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e4146-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="e4146-228">Barındırılan hizmet, uygulama başlangıcında bir kez etkinleştirilir ve uygulama kapatılırken düzgün şekilde kapanır.</span><span class="sxs-lookup"><span data-stu-id="e4146-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="e4146-229">Arka plan görevinin yürütülmesi sırasında bir hata oluşursa, `Dispose` `StopAsync` çağrılmadıysa bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e4146-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="e4146-230">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="e4146-230">Timed background tasks</span></span>

<span data-ttu-id="e4146-231">Zamanlanmış bir arka plan görevi, [System. Threading. Timer](xref:System.Threading.Timer) sınıfından kullanımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="e4146-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="e4146-232">Zamanlayıcı, görevin `DoWork` metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="e4146-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="e4146-233">Zamanlayıcı devre dışı bırakılır `StopAsync` ve hizmet kapsayıcısı bırakıldığında bırakıldı `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="e4146-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="e4146-234">, <xref:System.Threading.Timer> Önceki yürütmelerin bitmesini beklemez, bu `DoWork` nedenle gösterilen yaklaşım her senaryo için uygun olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e4146-234">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="e4146-235">Hizmet, `Startup.ConfigureServices` `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e4146-235">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="e4146-236">Bir arka plan görevinde kapsamlı bir hizmeti kullanma</span><span class="sxs-lookup"><span data-stu-id="e4146-236">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="e4146-237">[Kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) bir içinde kullanmak için `IHostedService` bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e4146-237">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="e4146-238">Barındırılan hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="e4146-238">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="e4146-239">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="e4146-239">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="e4146-240">Aşağıdaki örnekte, <xref:Microsoft.Extensions.Logging.ILogger> hizmetine bir hizmet eklenmiş olur:</span><span class="sxs-lookup"><span data-stu-id="e4146-240">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="e4146-241">Barındırılan hizmet, kendi metodunu çağırmak için kapsamlı arka plan görev hizmetini çözümlemek üzere bir kapsam oluşturur `DoWork` :</span><span class="sxs-lookup"><span data-stu-id="e4146-241">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="e4146-242">Hizmetler ' de kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e4146-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e4146-243">`IHostedService`Uygulama, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e4146-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="e4146-244">Sıraya alınan arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="e4146-244">Queued background tasks</span></span>

<span data-ttu-id="e4146-245">Arka plan görev kuyruğu .NET Framework 4. x ' i temel alır <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([ASP.NET Core için yerleşik olarak zamanlanmış olarak zamanlandı](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="e4146-245">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="e4146-246">' De `QueueHostedService` , kuyruktaki arka plan görevleri, uzun süre çalışan uygulama için temel bir sınıf olan bir [backgroundservice](#backgroundservice-base-class)olarak kuyruğa alınır ve yürütülür `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="e4146-246">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="e4146-247">Hizmetler ' de kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e4146-247">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e4146-248">`IHostedService`Uygulama, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e4146-248">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="e4146-249">Dizin sayfası model sınıfında:</span><span class="sxs-lookup"><span data-stu-id="e4146-249">In the Index page model class:</span></span>

* <span data-ttu-id="e4146-250">`IBackgroundTaskQueue`Oluşturucuya eklenir ve öğesine atanır `Queue` .</span><span class="sxs-lookup"><span data-stu-id="e4146-250">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="e4146-251"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Eklenen ve öğesine atandı `_serviceScopeFactory` .</span><span class="sxs-lookup"><span data-stu-id="e4146-251">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="e4146-252">Fabrika, <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsam içinde hizmet oluşturmak için kullanılan örnekleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e4146-252">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="e4146-253">Uygulama (bir tekil `AppDbContext` hizmet) içinde veritabanı kayıtları yazmak için uygulamanın ( [kapsamlı bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes)) kullanılabilmesi için bir kapsam oluşturulur `IBackgroundTaskQueue` .</span><span class="sxs-lookup"><span data-stu-id="e4146-253">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="e4146-254">Dizin sayfasında **Görev Ekle** düğmesi seçildiğinde `OnPostAddTask` Yöntem yürütülür.</span><span class="sxs-lookup"><span data-stu-id="e4146-254">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="e4146-255">`QueueBackgroundWorkItem`bir iş öğesini kuyruğa almak için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="e4146-255">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e4146-256">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e4146-256">Additional resources</span></span>

* [<span data-ttu-id="e4146-257">Ihostedservice ve BackgroundService sınıfıyla mikro hizmetlerde arka plan görevleri uygulama</span><span class="sxs-lookup"><span data-stu-id="e4146-257">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="e4146-258">Azure App Service Web Işleri ile arka plan görevleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="e4146-258">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
