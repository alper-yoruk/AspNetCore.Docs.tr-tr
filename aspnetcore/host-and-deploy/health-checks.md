---
title: ASP.NET Core'da sağlık kontrolleri
author: rick-anderson
description: Uygulamalar ve veritabanları gibi ASP.NET Core altyapısı için sistem durumu denetimlerini nasıl ayarlayacağınız hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664887"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="662f9-103">ASP.NET Core'da sağlık kontrolleri</span><span class="sxs-lookup"><span data-stu-id="662f9-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="662f9-104">Yazar: [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="662f9-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="662f9-105">ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu bildirmek için Sağlık Denetimleri Middleware ve kütüphaneler sunar.</span><span class="sxs-lookup"><span data-stu-id="662f9-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="662f9-106">Sağlık kontrolleri HTTP uç noktaları olarak bir uygulama tarafından ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="662f9-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="662f9-107">Sistem durumu denetimi uç noktaları çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="662f9-108">Sistem durumu sondaları, bir uygulamanın durumunu kontrol etmek için konteyner orkestratörleri ve yük dengeleyicileri tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="662f9-109">Örneğin, bir kapsayıcı orkestratör, yuvarlanan dağıtımı durdurarak veya bir kapsayıcıyı yeniden başlatarak başarısız bir sistem durumu denetimine yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="662f9-110">Yük dengeleyicisi, trafiği başarısız örnekten sağlıklı bir örneğe yönlendirme yaparak sağlıksız bir uygulamaya tepki verebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="662f9-111">Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı durum için izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="662f9-112">Sistem durumu denetimleri, kullanılabilirliği ve normal işleyişi onaylamak için bir uygulamanın veritabanları ve harici hizmet bitiş noktaları gibi bağımlılıklarını sınayabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="662f9-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="662f9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="662f9-114">Örnek uygulama, bu konuda açıklanan senaryoörnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="662f9-115">Belirli bir senaryo için örnek uygulamayı çalıştırmak için, proje klasöründeki [dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutunu komut kabuğunda kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="662f9-116">Örnek uygulamanın nasıl kullanılacağıyla ilgili ayrıntılar için örnek uygulamanın *README.md* dosyasına ve bu konudaki senaryo açıklamalarına bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="662f9-117">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="662f9-117">Prerequisites</span></span>

<span data-ttu-id="662f9-118">Sistem durumu denetimleri genellikle bir uygulamanın durumunu kontrol etmek için harici bir izleme hizmeti veya konteyner orchestrator ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="662f9-119">Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağına karar verin.</span><span class="sxs-lookup"><span data-stu-id="662f9-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="662f9-120">İzleme sistemi, hangi sistem durumu denetimlerinin oluşturulmasını ve uç noktalarının nasıl yapılandırılabildiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="662f9-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="662f9-121">[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketi ASP.NET Core uygulamaları için dolaylı olarak başvurulmuyorum.</span><span class="sxs-lookup"><span data-stu-id="662f9-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="662f9-122">Entity Framework Core kullanarak sistem durumu denetimleri gerçekleştirmek için [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="662f9-123">Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek için başlangıç kodu sağlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="662f9-124">[Veritabanı sonda](#database-probe) senaryosu [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının durumunu denetler.</span><span class="sxs-lookup"><span data-stu-id="662f9-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="662f9-125">[DbContext sonda](#entity-framework-core-dbcontext-probe) senaryosu bir EF `DbContext`Core kullanarak bir veritabanı denetler.</span><span class="sxs-lookup"><span data-stu-id="662f9-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="662f9-126">Veritabanı senaryolarını incelemek için örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="662f9-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="662f9-127">Bir veritabanı oluşturur ve *appsettings.json* dosyasında bağlantı dizesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="662f9-128">Proje dosyasında aşağıdaki paket başvuruları vardır:</span><span class="sxs-lookup"><span data-stu-id="662f9-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="662f9-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="662f9-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="662f9-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="662f9-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="662f9-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="662f9-132">Başka bir sistem durumu denetimi senaryosu, sistem durumu denetimlerinin yönetim bağlantı noktasına nasıl filtre uygulandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="662f9-133">Örnek uygulama, yönetim URL'sini ve yönetim bağlantı noktasını içeren bir *Properties/launchSettings.json* dosyası oluşturmanızı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="662f9-134">Daha fazla bilgi için bağlantı noktası bölümüne [göre Filtre'ye](#filter-by-port) bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="662f9-135">Temel sağlık sondası</span><span class="sxs-lookup"><span data-stu-id="662f9-135">Basic health probe</span></span>

<span data-ttu-id="662f9-136">Birçok uygulama için, uygulamanın istekleri *(canlılık)* işlemek için kullanılabilirliğini bildiren temel bir sağlık sondası yapılandırması, uygulamanın durumunu keşfetmek için yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="662f9-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="662f9-137">Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu yanıtıyla bir URL bitiş noktasında yanıt vermesi için Sistem Durumu Denetimleri Aracı'nı çağırır.</span><span class="sxs-lookup"><span data-stu-id="662f9-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="662f9-138">Varsayılan olarak, belirli bir bağımlılık veya alt sistemi sınamak için belirli bir sistem denetimi kaydedilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="662f9-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="662f9-139">Uygulama, sistem durumu bitiş noktası URL'sinde yanıt verebiliyorsa sağlıklı kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="662f9-140">Varsayılan yanıt yazarı durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ) istemciye düz metin yanıtı olarak yazar ve bu yanıtı [bir HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus.Nothealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durumunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="662f9-141">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-142">'yi arayarak `MapHealthChecks` bir sistem `Startup.Configure`durumu denetimi bitiş noktası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="662f9-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="662f9-143">Örnek uygulamada, sağlık kontrolü bitiş noktası `/health` *(BasicStartup.cs)* olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="662f9-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="662f9-144">Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="662f9-145">Docker örneği</span><span class="sxs-lookup"><span data-stu-id="662f9-145">Docker example</span></span>

<span data-ttu-id="662f9-146">[Docker,](xref:host-and-deploy/docker/index) temel sistem `HEALTHCHECK` durumu denetimi yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilecek yerleşik bir yönerge sunar:</span><span class="sxs-lookup"><span data-stu-id="662f9-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="662f9-147">Sistem durumu denetimleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="662f9-147">Create health checks</span></span>

<span data-ttu-id="662f9-148">Sistem durumu denetimleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> arabirimi uygulanarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="662f9-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="662f9-149">Yöntem, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> durumu `Healthy`, `Degraded`, veya `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="662f9-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="662f9-150">Sonuç, yapılandırılabilir durum koduyla düz metin yanıtı olarak yazılır (yapılandırma [Sistem Durumu denetim seçenekleri](#health-check-options) bölümünde açıklanır).</span><span class="sxs-lookup"><span data-stu-id="662f9-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="662f9-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>isteğe bağlı anahtar değeri çiftleri de döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="662f9-152">Aşağıdaki `ExampleHealthCheck` sınıf, sistem durumu denetiminin düzenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="662f9-153">Sistem durumu denetimi mantığı `CheckHealthAsync` yönteme yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="662f9-154">Aşağıdaki örnek, bir kukla `healthCheckResultHealthy`değişken, `true`için .</span><span class="sxs-lookup"><span data-stu-id="662f9-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="662f9-155">Değeri `healthCheckResultHealthy` , `false` [HealthCheckResult.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum olarak ayarlanırsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="662f9-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="662f9-156">Sistem durumu kontrol hizmetlerini kaydedin</span><span class="sxs-lookup"><span data-stu-id="662f9-156">Register health check services</span></span>

<span data-ttu-id="662f9-157">Türü `ExampleHealthCheck` ile <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> sağlık kontrol hizmetlerine `Startup.ConfigureServices`eklenir:</span><span class="sxs-lookup"><span data-stu-id="662f9-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="662f9-158">Aşağıdaki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> örnekte gösterilen aşırı yük, sistem<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>durumu denetimi bir hata bildirdiğinde bildirmek için hata durumunu ( ) ayarlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="662f9-159">Hata durumu `null` (varsayılan) olarak ayarlanmışsa, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="662f9-160">Bu aşırı yükleme, sistem durumu denetimi uygulaması ayarı onurlandırırsa, sistem durumu denetimi hatası oluştuğunda kitaplık tarafından gösterilen hata durumunun uygulama tarafından zorlandığı kitaplık yazarları için yararlı bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="662f9-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="662f9-161">*Etiketler,* sistem durumu denetimlerini filtrelemek için kullanılabilir [(Filtre sistem durumu denetimleri](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).</span><span class="sxs-lookup"><span data-stu-id="662f9-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="662f9-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lambda işlevini de yerine getirebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="662f9-163">Aşağıdaki örnekte, sistem durumu denetimi `Example` adı olarak belirtilir ve denetim her zaman sağlıklı bir durum döndürür:</span><span class="sxs-lookup"><span data-stu-id="662f9-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="662f9-164">Bağımsız <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> değişkenleri sistem durumu denetimi uygulamasına geçirmek için arayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="662f9-165">Aşağıdaki örnekte, `TestHealthCheckWithArgs` çağrıldığında <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> bir tamsayı ve kullanılacak bir dize kabul eder:</span><span class="sxs-lookup"><span data-stu-id="662f9-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="662f9-166">`TestHealthCheckWithArgs`tamsayı ve `AddTypeActivatedCheck` dize ile arayarak kaydedilir uygulamaya geçti:</span><span class="sxs-lookup"><span data-stu-id="662f9-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="662f9-167">Sağlık Denetimleri Yönlendirme'yi kullanma</span><span class="sxs-lookup"><span data-stu-id="662f9-167">Use Health Checks Routing</span></span>

<span data-ttu-id="662f9-168">In, `Startup.Configure` `MapHealthChecks` bitiş noktası URL'si veya göreli yolu olan bitiş noktası oluşturucuyu arayın:</span><span class="sxs-lookup"><span data-stu-id="662f9-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="662f9-169">Ana bilgisayar gerektirir</span><span class="sxs-lookup"><span data-stu-id="662f9-169">Require host</span></span>

<span data-ttu-id="662f9-170">Sistem `RequireHost` durumu denetimi bitiş noktası için bir veya daha fazla izin verilen ana bilgisayar belirtmek için arayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="662f9-171">Ana bilgisayarlar punycode yerine Unicode olmalıdır ve bir bağlantı noktası içerebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="662f9-172">Bir koleksiyon sağlanmazsa, herhangi bir ana bilgisayar kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="662f9-173">Daha fazla bilgi için bağlantı noktası bölümüne [göre Filtre'ye](#filter-by-port) bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="662f9-174">Yetkilendirme gerektirme</span><span class="sxs-lookup"><span data-stu-id="662f9-174">Require authorization</span></span>

<span data-ttu-id="662f9-175">Sistem `RequireAuthorization` durumu denetimi isteği bitiş noktasında Yetkilendirme Middleware'i çalıştırmak için arayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="662f9-176">Aşırı `RequireAuthorization` yükleme, bir veya daha fazla yetkilendirme ilkesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="662f9-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="662f9-177">Bir ilke sağlanmadıysa, varsayılan yetkilendirme ilkesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="662f9-178">Kaynaklar Arası İstekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="662f9-179">Bir tarayıcıdan el ile sistem durumu denetimleri gerçekleştirmek yaygın bir kullanım senaryosu `RequireCors` olmasa da, CORS Middleware sistem durumu denetimleri uç noktalarını arayarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="662f9-180">Aşırı `RequireCors` yükleme, CORS ilke oluşturucu temsilcisini ( )`CorsPolicyBuilder`veya bir ilke adı kabul eder.</span><span class="sxs-lookup"><span data-stu-id="662f9-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="662f9-181">Bir ilke sağlanmadıysa, varsayılan CORS ilkesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="662f9-182">Daha fazla bilgi için bkz. <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="662f9-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="662f9-183">Sistem durumu denetimi seçenekleri</span><span class="sxs-lookup"><span data-stu-id="662f9-183">Health check options</span></span>

<span data-ttu-id="662f9-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirme kiçin bir fırsat sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="662f9-185">Filtre sağlık denetimleri</span><span class="sxs-lookup"><span data-stu-id="662f9-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="662f9-186">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="662f9-187">Önbellek üstbilgilerini bastırma</span><span class="sxs-lookup"><span data-stu-id="662f9-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="662f9-188">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="662f9-189">Filtre sağlık denetimleri</span><span class="sxs-lookup"><span data-stu-id="662f9-189">Filter health checks</span></span>

<span data-ttu-id="662f9-190">Varsayılan olarak, Sistem Durumu Denetimleri Middleware tüm kayıtlı sistem durumu denetimlerini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="662f9-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="662f9-191">Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> seçene boolean döndüren bir işlev sağlayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="662f9-192">Aşağıdaki örnekte, `Bar` sistem durumu denetimi işlevin koşullu deyimindeki`bar_tag`etiketiyle filtrelenir `true` ve burada yalnızca sağlık denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşirse `foo_tag` veya: `baz_tag`</span><span class="sxs-lookup"><span data-stu-id="662f9-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="662f9-193">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="662f9-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="662f9-194">In `Startup.Configure`, `Predicate` filtreler 'Bar' sağlık kontrolü dışarı.</span><span class="sxs-lookup"><span data-stu-id="662f9-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="662f9-195">Sadece Foo ve Baz infaz.:</span><span class="sxs-lookup"><span data-stu-id="662f9-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="662f9-196">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-196">Customize the HTTP status code</span></span>

<span data-ttu-id="662f9-197">Sağlık <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> durumunun eşlemasını HTTP durum kodlarına göre özelleştirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="662f9-198">Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="662f9-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="662f9-199">Gereksinimlerinizi karşılamak için durum kodu değerlerini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="662f9-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="662f9-200">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="662f9-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="662f9-201">Önbellek üstbilgilerini bastırma</span><span class="sxs-lookup"><span data-stu-id="662f9-201">Suppress cache headers</span></span>

<span data-ttu-id="662f9-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem Durumu Denetimleri Middleware yanıt önbelleğe önlemek için bir sonda yanıtı http üstleri ekleyip ekledi denetimleri.</span><span class="sxs-lookup"><span data-stu-id="662f9-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="662f9-203">Değer (varsayılan) `false` ise, yanıt önbelleğini önlemek için ara yazılım `Cache-Control`, ve `Expires` `Pragma` üstbilgi kümeler veya üstbilgi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="662f9-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="662f9-204">Değer ise, `true`ara yazılım yanıtın önbellek üstbilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="662f9-205">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="662f9-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="662f9-206">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-206">Customize output</span></span>

<span data-ttu-id="662f9-207">In, `Startup.Configure`yanıt yazmak için bir temsilci için [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) seçeneğini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="662f9-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="662f9-208">Varsayılan [temsilci, HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değeriyle en az düz metin yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="662f9-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="662f9-209">Aşağıdaki özel temsilciler özel bir JSON yanıtı çıktı.</span><span class="sxs-lookup"><span data-stu-id="662f9-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="662f9-210">Örnek uygulamadan ilk örnek nasıl kullanılacağını <xref:System.Text.Json?displayProperty=fullName>gösterir:</span><span class="sxs-lookup"><span data-stu-id="662f9-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="662f9-211">İkinci örnek [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="662f9-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="662f9-212">Örnek `SYSTEM_TEXT_JSON` uygulamada, `Newtonsoft.Json` sürümünü etkinleştirmek için `WriteResponse` [CustomWriterStartup.cs'daki önişlemci yönergesini](xref:index#preprocessor-directives-in-sample-code) yorumla. *CustomWriterStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="662f9-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="662f9-213">Sistem durumu denetimleri API, karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz, çünkü biçim izleme sistemi seçtiğinize özgüdür.</span><span class="sxs-lookup"><span data-stu-id="662f9-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="662f9-214">Yanıtı gerektiğinde önceki örneklerde özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="662f9-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="662f9-215">JSON serileştirme hakkında daha `System.Text.Json`fazla bilgi için , [json'u .NET'te nasıl seri hale getirmek ve deserialize etmek için](/dotnet/standard/serialization/system-text-json-how-to)bkz.</span><span class="sxs-lookup"><span data-stu-id="662f9-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="662f9-216">Veritabanı sondası</span><span class="sxs-lookup"><span data-stu-id="662f9-216">Database probe</span></span>

<span data-ttu-id="662f9-217">Sistem durumu denetimi, veritabanının normal yanıt verilip vermeyiş olmadığını belirtmek için boolean testi olarak çalışacak bir veritabanı sorgusu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="662f9-218">Örnek uygulama, bir SQL Server veritabanında sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamaları için bir sistem durumu denetimi kitaplığı olan [AspNetCore.Diagnostics.HealthChecks'i](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır.</span><span class="sxs-lookup"><span data-stu-id="662f9-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="662f9-219">`AspNetCore.Diagnostics.HealthChecks`veritabanına `SELECT 1` bağlantının sağlıklı olduğunu doğrulamak için veritabanına karşı bir sorgu yürütür.</span><span class="sxs-lookup"><span data-stu-id="662f9-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="662f9-220">Bir sorguyla veritabanı bağlantısını denetlerken, hızla dönen bir sorgu seçin.</span><span class="sxs-lookup"><span data-stu-id="662f9-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="662f9-221">Sorgu yaklaşımı veritabanıaşırı yükleme ve performansını düşürme riskini çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="662f9-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="662f9-222">Çoğu durumda, bir test sorgusu çalıştırma gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="662f9-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="662f9-223">Yalnızca veritabanına başarılı bir bağlantı yapmak yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="662f9-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="662f9-224">Bir sorgu çalıştırmak için gerekli bulursanız, gibi `SELECT 1`basit bir SELECT sorgusu seçin.</span><span class="sxs-lookup"><span data-stu-id="662f9-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="662f9-225">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)için bir paket referans ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="662f9-226">Örnek uygulamanın *appsettings.json* dosyasında geçerli bir veritabanı bağlantı dizesi verin.</span><span class="sxs-lookup"><span data-stu-id="662f9-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="662f9-227">Uygulama, aşağıdakiler adlı `HealthCheckSample`bir SQL Server veritabanı kullanır:</span><span class="sxs-lookup"><span data-stu-id="662f9-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="662f9-228">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-229">Örnek uygulama veritabanının bağlantı dizesiyle `AddSqlServer` yöntemi çağırır *(DbHealthStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="662f9-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="662f9-230">Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası arayarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="662f9-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="662f9-231">Örnek uygulamayı kullanarak veritabanı sondasenaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="662f9-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="662f9-233">Varlık Çerçeve Çekirdek DbContext sondası</span><span class="sxs-lookup"><span data-stu-id="662f9-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="662f9-234">Denetim, `DbContext` uygulamanın BIR EF Core `DbContext`için yapılandırılan veritabanıyla iletişim kurabileceğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="662f9-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="662f9-235">Denetim `DbContext` aşağıdaki uygulamalarda desteklenir:</span><span class="sxs-lookup"><span data-stu-id="662f9-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="662f9-236">[Varlık Çerçevesi (EF) Çekirdeğini](/ef/core/)Kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="662f9-237">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)için bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="662f9-238">`AddDbContextCheck<TContext>`için `DbContext`bir sağlık kontrolü kaydeder.</span><span class="sxs-lookup"><span data-stu-id="662f9-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="662f9-239">Yöntem `DbContext` olarak `TContext` verilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="662f9-240">Hata durumunu, etiketleri ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="662f9-241">Varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="662f9-241">By default:</span></span>

* <span data-ttu-id="662f9-242">EF `DbContextHealthCheck` Core'un `CanConnectAsync` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="662f9-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="662f9-243">Aşırı yükleme yöntemini kullanarak `AddDbContextCheck` sistem durumu denetlerken hangi işlemin çalıştırılabileceğini özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="662f9-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="662f9-244">Sistem durumu denetiminin adı `TContext` türün adıdır.</span><span class="sxs-lookup"><span data-stu-id="662f9-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="662f9-245">Örnek uygulamada, `AppDbContext` *(DbContextHealthStartup.cs)* bir hizmet olarak `Startup.ConfigureServices` sağlanır `AddDbContextCheck` ve bir hizmet olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="662f9-246">Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası arayarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="662f9-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="662f9-247">Örnek uygulamayı `DbContext` kullanarak sonda senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde bulunmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="662f9-248">Veritabanı varsa, silin.</span><span class="sxs-lookup"><span data-stu-id="662f9-248">If the database exists, delete it.</span></span>

<span data-ttu-id="662f9-249">Proje klasöründen komut kabuğunda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="662f9-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="662f9-250">Uygulama çalışmaya devam ettikten sonra, tarayıcıda `/health` bitiş noktasına istekte bulunarak sistem durumunu kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="662f9-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="662f9-251">Veritabanı ve `AppDbContext` yok, bu nedenle uygulama aşağıdaki yanıtı sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="662f9-252">Veritabanını oluşturmak için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="662f9-253">Bir istekte `/createdatabase`bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="662f9-254">Uygulama şu yanıtı verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="662f9-255">`/health` Bitiş noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="662f9-256">Veritabanı ve bağlam vardır, bu nedenle uygulama yanıt verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="662f9-257">Veritabanını silmek için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="662f9-258">Bir istekte `/deletedatabase`bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="662f9-259">Uygulama şu yanıtı verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="662f9-260">`/health` Bitiş noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="662f9-261">Uygulama sağlıksız bir yanıt sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="662f9-262">Ayrı hazırlık ve canlılık sondaları</span><span class="sxs-lookup"><span data-stu-id="662f9-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="662f9-263">Bazı barındırma senaryolarında, iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="662f9-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="662f9-264">Uygulama çalışıyor ama henüz istek almaya hazır değil.</span><span class="sxs-lookup"><span data-stu-id="662f9-264">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="662f9-265">Bu durum uygulamanın *hazır olmasıdır.*</span><span class="sxs-lookup"><span data-stu-id="662f9-265">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="662f9-266">Uygulama çalışıyor ve isteklere yanıt veriyor.</span><span class="sxs-lookup"><span data-stu-id="662f9-266">The app is functioning and responding to requests.</span></span> <span data-ttu-id="662f9-267">Bu durum uygulamanın *canlılığıdır.*</span><span class="sxs-lookup"><span data-stu-id="662f9-267">This state is the app's *liveness*.</span></span>

<span data-ttu-id="662f9-268">Hazırlık denetimi genellikle uygulamanın tüm alt sistemlerinin ve kaynaklarının kullanılabilir olup olmadığını belirlemek için daha kapsamlı ve zaman alan bir denetim ler kümesi gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-268">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="662f9-269">Canlılık denetimi, uygulamanın istekleri işlemek için kullanılabilir olup olmadığını belirlemek için yalnızca hızlı bir denetim gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-269">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="662f9-270">Uygulama hazırlık kontrolünden geçtikten sonra, pahalı hazırlık kontrolleri&mdash;seti ile uygulamayı daha fazla yükmeye gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="662f9-270">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="662f9-271">Örnek uygulama, [Barındırılan Hizmette](xref:fundamentals/host/hosted-services)uzun süren başlangıç görevinin tamamlanamasını bildirmek için bir sistem durumu denetimi içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="662f9-272">Barındırılan `StartupHostedServiceHealthCheck` hizmetin `StartupTaskCompleted`uzun süren görevi tamamlandığında `true` ayarlanabileceği bir özelliği ortaya çıkarır *(StartupHostedServiceHealthCheck.cs):*</span><span class="sxs-lookup"><span data-stu-id="662f9-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="662f9-273">Uzun süren arka plan görevi [Barındırılan Hizmet](xref:fundamentals/host/hosted-services) *(Hizmetler/BaşlangıçHostedService)* tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="662f9-274">Görevin sonunda, `StartupHostedServiceHealthCheck.StartupTaskCompleted` `true`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="662f9-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="662f9-275">Sağlık kontrolü, barındırılan hizmetle <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` birlikte kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="662f9-276">Barındırılan hizmet in sağlık kontrolünde özelliği ayarlamak gerekir, çünkü sağlık kontrolü de hizmet konteyner *(LivenessProbeStartup.cs)* kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="662f9-277">Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası' nı arayarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="662f9-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="662f9-278">Örnek uygulamada, sistem durumu denetimi uç noktaları aşağıdaki noktalarda oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="662f9-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="662f9-279">`/health/ready`hazırlık kontrolü için.</span><span class="sxs-lookup"><span data-stu-id="662f9-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="662f9-280">Hazır kontrol, sağlık denetimlerini etiketle birlikte `ready` sistem durumu denetimine filtreler.</span><span class="sxs-lookup"><span data-stu-id="662f9-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="662f9-281">`/health/live`canlılık kontrolü için.</span><span class="sxs-lookup"><span data-stu-id="662f9-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="662f9-282">Canlılık `StartupHostedServiceHealthCheck` `false` [denetimi, HealthCheckOptions.Predicate'de](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) dönerek filtreler (daha fazla bilgi için [bkz.](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="662f9-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="662f9-283">Aşağıdaki örnek kodda:</span><span class="sxs-lookup"><span data-stu-id="662f9-283">In the following example code:</span></span>

* <span data-ttu-id="662f9-284">Hazırlık denetimi, 'hazır' etiketiyle tüm kayıtlı çekleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="662f9-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="662f9-285">Tüm `Predicate` çekleri hariç tutar ve 200-Ok döndürün.</span><span class="sxs-lookup"><span data-stu-id="662f9-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="662f9-286">Örnek uygulamayı kullanarak hazırlık/canlılık yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="662f9-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="662f9-287">Bir tarayıcıda, `/health/ready` 15 saniye geçene kadar birkaç kez ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="662f9-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="662f9-288">Sağlık kontrolü ilk 15 saniye *sağlıksız* bildirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="662f9-289">15 saniye sonra, bitiş noktası barındırılan hizmet tarafından uzun süren görevin tamamlanmasını yansıtan *Sağlıklı*raporlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="662f9-290">Bu örnek, iki saniyelik<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> bir gecikmeyle ilk hazırlık denetimini çalıştıran bir Sağlık Denetimi Yayımcısı (uygulama) da oluşturur.</span><span class="sxs-lookup"><span data-stu-id="662f9-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="662f9-291">Daha fazla bilgi için [Sağlık Denetimi Yayımcısı](#health-check-publisher) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="662f9-292">Kubernetes örneği</span><span class="sxs-lookup"><span data-stu-id="662f9-292">Kubernetes example</span></span>

<span data-ttu-id="662f9-293">Ayrı hazırlık ve canlılık denetimleri kullanarak [Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="662f9-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="662f9-294">Kubernetes'te, bir uygulamanın, temel veritabanı kullanılabilirliğinin sınanması gibi istekleri kabul etmeden önce zaman alan başlangıç çalışması gerçekleştirmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="662f9-295">Ayrı denetimler kullanmak, orkestratörün uygulamanın çalışıp çalışmadığını ancak henüz hazır olup olmadığını veya uygulamanın başlatılıp başlatılamadığını ayırt etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="662f9-296">Kubernetes'teki hazırlık ve canlılık sondaları hakkında daha fazla bilgi için Kubernetes belgelerinde [Canlılık ve Hazırlık Sondalarını Yapılandırıyorum'a](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="662f9-297">Aşağıdaki örnek, Bir Kubernetes hazırlık sondası yapılandırmasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="662f9-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="662f9-298">Özel yanıt yazarı ile metrik tabanlı sonda</span><span class="sxs-lookup"><span data-stu-id="662f9-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="662f9-299">Örnek uygulama, özel bir yanıt yazarıyla bir bellek durumu denetimi gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="662f9-300">`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) daha fazlasını kullanıyorsa, bozulmuş bir durum bildirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="662f9-301">Uygulama <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> için Çöp Toplayıcı (GC) bilgileri *(MemoryHealthCheck.cs)* içerir:</span><span class="sxs-lookup"><span data-stu-id="662f9-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="662f9-302">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-303">Sağlık kontrolünü sağlayarak etkinleştirmek <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>yerine, `MemoryHealthCheck` hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="662f9-304">Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler sağlık kontrol hizmetleri ve ara yazılımlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="662f9-305">Sağlık kontrolü hizmetlerini Singleton hizmetleri olarak kaydetmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="662f9-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="662f9-306">Örnek uygulamanın *CustomWriterStartup.cs:*</span><span class="sxs-lookup"><span data-stu-id="662f9-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="662f9-307">Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası' nı arayarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="662f9-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="662f9-308">Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter özelliğini, sistem durumu denetimi yürütürken özel bir JSON yanıtı çıktısı için> <bir `WriteResponse` temsilci sağlanır:</span><span class="sxs-lookup"><span data-stu-id="662f9-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="662f9-309">Temsilci `WriteResponse` bir JSON nesnesi `CompositeHealthCheckResult` içine biçimlendirin ve sistem durumu denetimi yanıtı için JSON çıktısı verir.</span><span class="sxs-lookup"><span data-stu-id="662f9-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="662f9-310">Daha fazla bilgi için [çıktıyı Özelleştir](#customize-output) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="662f9-311">Örnek uygulamayı kullanarak özel yanıt yazar çıktısı ile metrik tabanlı sondayı çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="662f9-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="662f9-312">[AspNetCore.Diagnostics.HealthChecks,](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) disk depolama ve maksimum değer canlılık denetimleri de dahil olmak üzere metrik tabanlı sistem durumu denetimi senaryoları içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="662f9-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="662f9-314">Bağlantı noktasına göre filtrele</span><span class="sxs-lookup"><span data-stu-id="662f9-314">Filter by port</span></span>

<span data-ttu-id="662f9-315">Sistem `RequireHost` `MapHealthChecks` durumu denetimi isteklerini belirtilen bağlantı noktasıyla sınırlamak için bir bağlantı noktası belirten bir URL deseniyle arayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="662f9-316">Bu genellikle hizmetleri izlemek için bir bağlantı noktası ortaya çıkarmak için bir kapsayıcı ortamında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="662f9-317">Örnek uygulama, Çevre Değişken [yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="662f9-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="662f9-318">Bağlantı noktası *launchSettings.json* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına aktarılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="662f9-319">Ayrıca, sunucuyu yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="662f9-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="662f9-320">Yönetim bağlantı noktası yapılandırmasını göstermek için örnek uygulamayı kullanmak için, *özellikler* klasöründe *launchSettings.json* dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="662f9-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="662f9-321">Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings.json* dosyası örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulmalıdır:</span><span class="sxs-lookup"><span data-stu-id="662f9-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="662f9-322">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-323">'yi arayarak `MapHealthChecks` bir sistem `Startup.Configure`durumu denetimi bitiş noktası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="662f9-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="662f9-324">Örnek uygulamada, bitiş noktasında `RequireHost` ki bir `Startup.Configure` çağrı yönetim bağlantı noktasını yapılandırmadan belirtir:</span><span class="sxs-lookup"><span data-stu-id="662f9-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="662f9-325">Son noktalar örnek uygulamada `Startup.Configure`'da oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="662f9-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="662f9-326">Aşağıdaki örnek kodda:</span><span class="sxs-lookup"><span data-stu-id="662f9-326">In the following example code:</span></span>

* <span data-ttu-id="662f9-327">Hazırlık denetimi, 'hazır' etiketiyle tüm kayıtlı çekleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="662f9-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="662f9-328">Tüm `Predicate` çekleri hariç tutar ve 200-Ok döndürün.</span><span class="sxs-lookup"><span data-stu-id="662f9-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="662f9-329">Yönetim bağlantı noktasını açıkça kodda ayarlayarak örnek uygulamada *launchSettings.json* dosyasını oluşturmaktan kaçınabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="662f9-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="662f9-330">Oluşturulan *Program.cs,* <xref:Microsoft.Extensions.Hosting.HostBuilder> bir çağrı ekleyin <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> ve uygulamanın yönetim bağlantı noktası bitiş noktasını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="662f9-331">`Configure` *ManagementPortStartup.cs*olarak, aşağıdakilerle yönetim bağlantı noktasını belirtin: `RequireHost`</span><span class="sxs-lookup"><span data-stu-id="662f9-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="662f9-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="662f9-332">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="662f9-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="662f9-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="662f9-334">Örnek uygulamayı kullanarak yönetim bağlantı noktası yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="662f9-335">Sistem durumu denetimi kitaplığı dağıtma</span><span class="sxs-lookup"><span data-stu-id="662f9-335">Distribute a health check library</span></span>

<span data-ttu-id="662f9-336">Sistem durumu denetimini kitaplık olarak dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="662f9-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="662f9-337"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> Arabirimi bağımsız bir sınıf olarak uygulayan bir sistem durumu denetimi yazın.</span><span class="sxs-lookup"><span data-stu-id="662f9-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="662f9-338">Sınıf [bağımlılık enjeksiyonu (DI),](xref:fundamentals/dependency-injection)tür etkinleştirme ve yapılandırma verilerine erişmek için [adlandırılmış seçeneklere](xref:fundamentals/configuration/options) güvenebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="662f9-339">Sağlık kontrolleri mantığı: `CheckHealthAsync`</span><span class="sxs-lookup"><span data-stu-id="662f9-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="662f9-340">`data1`ve `data2` sondanın sistem durumu kontrol mantığını çalıştırmak için yöntemde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="662f9-341">`AccessViolationException`işlenir.</span><span class="sxs-lookup"><span data-stu-id="662f9-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="662f9-342">Bir <xref:System.AccessViolationException> durum oluştuğunda, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> kullanıcıların <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> sistem durumu denetimleri hata durumunu yapılandırmasına izin vermek için döndürülür.</span><span class="sxs-lookup"><span data-stu-id="662f9-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="662f9-343">Tüketen uygulamanın yönteminde aradığı parametreleri `Startup.Configure` içeren bir uzantı yöntemi yazın.</span><span class="sxs-lookup"><span data-stu-id="662f9-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="662f9-344">Aşağıdaki örnekte, aşağıdaki sistem durumu denetimi yöntemi imzasını varsayalım:</span><span class="sxs-lookup"><span data-stu-id="662f9-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="662f9-345">Önceki imza, sistem `ExampleHealthCheck` durumu denetimi sondası mantığını işlemek için ek veri gerektirdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="662f9-346">Veriler, sistem durumu denetimi bir uzantı yöntemiyle kaydedildiğinde sistem durumu denetimi örneğini oluşturmak için kullanılan temsilciye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="662f9-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="662f9-347">Aşağıdaki örnekte, arayan isteğe bağlı olarak belirtir:</span><span class="sxs-lookup"><span data-stu-id="662f9-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="662f9-348">sağlık kontrol`name`adı ( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-348">health check name (`name`).</span></span> <span data-ttu-id="662f9-349">Eğer `null` `example_health_check` , kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="662f9-350">sistem durumu denetimi için`data1`dize veri noktası ( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="662f9-351">sistem durumu denetimi için sonda`data2`veri noktası ( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="662f9-352">Eğer `null` `1` , kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="662f9-353">hata durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="662f9-354">Varsayılan değer: `null`.</span><span class="sxs-lookup"><span data-stu-id="662f9-354">The default is `null`.</span></span> <span data-ttu-id="662f9-355">If `null`, [HealthStatus.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bir başarısızlık durumu için bildirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="662f9-356">etiketleri`IEnumerable<string>`( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="662f9-357">Sağlık Kontrolü Yayıncı</span><span class="sxs-lookup"><span data-stu-id="662f9-357">Health Check Publisher</span></span>

<span data-ttu-id="662f9-358">Servis <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> konteynerine bir şey eklendiğinde, sağlık kontrol sistemi sağlık `PublishAsync` kontrollerinizi düzenli aralıklarla yürütür ve sonuçla birlikte çağrıları yerine getirmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="662f9-359">Bu, her işlemin sistem durumunu belirlemek için izleme sistemini periyodik olarak aramasını bekleyen push tabanlı sistem senaryosunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="662f9-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="662f9-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Arabirimin tek bir yöntemi vardır:</span><span class="sxs-lookup"><span data-stu-id="662f9-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="662f9-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>ayarlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="662f9-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Örnekleri yürütmeden önce uygulama başladıktan sonra uygulanan ilk <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> gecikme.</span><span class="sxs-lookup"><span data-stu-id="662f9-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="662f9-363">Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemeler için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="662f9-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="662f9-364">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="662f9-364">The default value is five seconds.</span></span>
* <span data-ttu-id="662f9-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> süresi.</span><span class="sxs-lookup"><span data-stu-id="662f9-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="662f9-366">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="662f9-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="662f9-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> Varsa `null` (varsayılan), sistem durumu denetimi yayımcısı hizmeti tüm kayıtlı sistem durumu denetimlerini çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="662f9-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="662f9-368">Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, denetim kümesini filtreleyen bir işlev sağlayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="662f9-369">Yüklem her dönem değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="662f9-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnekler için sistem durumu denetimlerini yürütmek için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="662f9-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="662f9-371">Bir <xref:System.Threading.Timeout.InfiniteTimeSpan> zaman arası olmadan yürütmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="662f9-372">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="662f9-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="662f9-373">Örnek uygulamada, `ReadinessPublisher` bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="662f9-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="662f9-374">Sistem durumu denetimi durumu, her denetim için günlük düzeyinde günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="662f9-375">Bilgi<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) sağlık kontrolleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>durumu ise .</span><span class="sxs-lookup"><span data-stu-id="662f9-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="662f9-376">Hata<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) durum <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ya <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>da .</span><span class="sxs-lookup"><span data-stu-id="662f9-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="662f9-377">Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazırlık denetiminde iki saniyelik bir başlatma gecikmesi vardır ve denetimi her 30 saniyede bir çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="662f9-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="662f9-378"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Uygulamayı etkinleştirmek için, örnek `ReadinessPublisher` bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) konteynerinde tek tonlu bir hizmet olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="662f9-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) [Uygulama Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayıncılar içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="662f9-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="662f9-381">MapWhen ile sistem durumu denetimlerini kısıtlama</span><span class="sxs-lookup"><span data-stu-id="662f9-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="662f9-382">Sistem <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> durumu denetimi uç noktaları için istek ardışık dallandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="662f9-383">Aşağıdaki örnekte, `MapWhen` `api/HealthCheck` bitiş noktası için bir GET isteği alınırsa, Sağlık Denetimleri Ara ware etkinleştirmek için istek ardışık dalları:</span><span class="sxs-lookup"><span data-stu-id="662f9-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="662f9-384">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="662f9-384">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="662f9-385">ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu bildirmek için Sağlık Denetimleri Middleware ve kütüphaneler sunar.</span><span class="sxs-lookup"><span data-stu-id="662f9-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="662f9-386">Sağlık kontrolleri HTTP uç noktaları olarak bir uygulama tarafından ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="662f9-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="662f9-387">Sistem durumu denetimi uç noktaları çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="662f9-388">Sistem durumu sondaları, bir uygulamanın durumunu kontrol etmek için konteyner orkestratörleri ve yük dengeleyicileri tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="662f9-389">Örneğin, bir kapsayıcı orkestratör, yuvarlanan dağıtımı durdurarak veya bir kapsayıcıyı yeniden başlatarak başarısız bir sistem durumu denetimine yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="662f9-390">Yük dengeleyicisi, trafiği başarısız örnekten sağlıklı bir örneğe yönlendirme yaparak sağlıksız bir uygulamaya tepki verebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="662f9-391">Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı durum için izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="662f9-392">Sistem durumu denetimleri, kullanılabilirliği ve normal işleyişi onaylamak için bir uygulamanın veritabanları ve harici hizmet bitiş noktaları gibi bağımlılıklarını sınayabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="662f9-393">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="662f9-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="662f9-394">Örnek uygulama, bu konuda açıklanan senaryoörnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="662f9-395">Belirli bir senaryo için örnek uygulamayı çalıştırmak için, proje klasöründeki [dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutunu komut kabuğunda kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="662f9-396">Örnek uygulamanın nasıl kullanılacağıyla ilgili ayrıntılar için örnek uygulamanın *README.md* dosyasına ve bu konudaki senaryo açıklamalarına bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="662f9-397">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="662f9-397">Prerequisites</span></span>

<span data-ttu-id="662f9-398">Sistem durumu denetimleri genellikle bir uygulamanın durumunu kontrol etmek için harici bir izleme hizmeti veya konteyner orchestrator ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="662f9-399">Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağına karar verin.</span><span class="sxs-lookup"><span data-stu-id="662f9-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="662f9-400">İzleme sistemi, hangi sistem durumu denetimlerinin oluşturulmasını ve uç noktalarının nasıl yapılandırılabildiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="662f9-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="662f9-401">Başvuru [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) veya [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketine bir paket başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="662f9-402">Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek için başlangıç kodu sağlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="662f9-403">[Veritabanı sonda](#database-probe) senaryosu [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının durumunu denetler.</span><span class="sxs-lookup"><span data-stu-id="662f9-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="662f9-404">[DbContext sonda](#entity-framework-core-dbcontext-probe) senaryosu bir EF `DbContext`Core kullanarak bir veritabanı denetler.</span><span class="sxs-lookup"><span data-stu-id="662f9-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="662f9-405">Veritabanı senaryolarını incelemek için örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="662f9-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="662f9-406">Bir veritabanı oluşturur ve *appsettings.json* dosyasında bağlantı dizesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="662f9-407">Proje dosyasında aşağıdaki paket başvuruları vardır:</span><span class="sxs-lookup"><span data-stu-id="662f9-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="662f9-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="662f9-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="662f9-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="662f9-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="662f9-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="662f9-411">Başka bir sistem durumu denetimi senaryosu, sistem durumu denetimlerinin yönetim bağlantı noktasına nasıl filtre uygulandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="662f9-412">Örnek uygulama, yönetim URL'sini ve yönetim bağlantı noktasını içeren bir *Properties/launchSettings.json* dosyası oluşturmanızı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="662f9-413">Daha fazla bilgi için bağlantı noktası bölümüne [göre Filtre'ye](#filter-by-port) bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="662f9-414">Temel sağlık sondası</span><span class="sxs-lookup"><span data-stu-id="662f9-414">Basic health probe</span></span>

<span data-ttu-id="662f9-415">Birçok uygulama için, uygulamanın istekleri *(canlılık)* işlemek için kullanılabilirliğini bildiren temel bir sağlık sondası yapılandırması, uygulamanın durumunu keşfetmek için yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="662f9-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="662f9-416">Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu yanıtıyla bir URL bitiş noktasında yanıt vermesi için Sistem Durumu Denetimleri Aracı'nı çağırır.</span><span class="sxs-lookup"><span data-stu-id="662f9-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="662f9-417">Varsayılan olarak, belirli bir bağımlılık veya alt sistemi sınamak için belirli bir sistem denetimi kaydedilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="662f9-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="662f9-418">Uygulama, sistem durumu bitiş noktası URL'sinde yanıt verebiliyorsa sağlıklı kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="662f9-419">Varsayılan yanıt yazarı durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ) istemciye düz metin yanıtı olarak yazar ve bu yanıtı [bir HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus.Nothealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durumunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="662f9-420">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-421">İstek işleme ardışık boru hattı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> ile Sağlık Denetimleri Middleware için bir bitiş noktası `Startup.Configure`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="662f9-422">Örnek uygulamada, sağlık kontrolü bitiş noktası `/health` *(BasicStartup.cs)* olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="662f9-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="662f9-423">Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="662f9-424">Docker örneği</span><span class="sxs-lookup"><span data-stu-id="662f9-424">Docker example</span></span>

<span data-ttu-id="662f9-425">[Docker,](xref:host-and-deploy/docker/index) temel sistem `HEALTHCHECK` durumu denetimi yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilecek yerleşik bir yönerge sunar:</span><span class="sxs-lookup"><span data-stu-id="662f9-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="662f9-426">Sistem durumu denetimleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="662f9-426">Create health checks</span></span>

<span data-ttu-id="662f9-427">Sistem durumu denetimleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> arabirimi uygulanarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="662f9-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="662f9-428">Yöntem, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> durumu `Healthy`, `Degraded`, veya `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="662f9-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="662f9-429">Sonuç, yapılandırılabilir durum koduyla düz metin yanıtı olarak yazılır (yapılandırma [Sistem Durumu denetim seçenekleri](#health-check-options) bölümünde açıklanır).</span><span class="sxs-lookup"><span data-stu-id="662f9-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="662f9-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>isteğe bağlı anahtar değeri çiftleri de döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="662f9-431">Örnek sistem durumu denetimi</span><span class="sxs-lookup"><span data-stu-id="662f9-431">Example health check</span></span>

<span data-ttu-id="662f9-432">Aşağıdaki `ExampleHealthCheck` sınıf, sistem durumu denetiminin düzenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="662f9-433">Sistem durumu denetimi mantığı `CheckHealthAsync` yönteme yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="662f9-434">Aşağıdaki örnek, bir kukla `healthCheckResultHealthy`değişken, `true`için .</span><span class="sxs-lookup"><span data-stu-id="662f9-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="662f9-435">Değeri `healthCheckResultHealthy` , `false` [HealthCheckResult.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum olarak ayarlanırsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="662f9-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="662f9-436">Sistem durumu kontrol hizmetlerini kaydedin</span><span class="sxs-lookup"><span data-stu-id="662f9-436">Register health check services</span></span>

<span data-ttu-id="662f9-437">Tür, `ExampleHealthCheck` sağlık denetimi hizmetlerine `Startup.ConfigureServices` aşağıdakilerle <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>eklenir:</span><span class="sxs-lookup"><span data-stu-id="662f9-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="662f9-438">Aşağıdaki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> örnekte gösterilen aşırı yük, sistem<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>durumu denetimi bir hata bildirdiğinde bildirmek için hata durumunu ( ) ayarlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="662f9-439">Hata durumu `null` (varsayılan) olarak ayarlanmışsa, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="662f9-440">Bu aşırı yükleme, sistem durumu denetimi uygulaması ayarı onurlandırırsa, sistem durumu denetimi hatası oluştuğunda kitaplık tarafından gösterilen hata durumunun uygulama tarafından zorlandığı kitaplık yazarları için yararlı bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="662f9-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="662f9-441">*Etiketler,* sistem durumu denetimlerini filtrelemek için kullanılabilir [(Filtre sistem durumu denetimleri](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).</span><span class="sxs-lookup"><span data-stu-id="662f9-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="662f9-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lambda işlevini de yerine getirebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="662f9-443">Aşağıdaki `Startup.ConfigureServices` örnekte, sistem durumu denetimi `Example` adı olarak belirtilir ve denetim her zaman sağlıklı bir durum döndürür:</span><span class="sxs-lookup"><span data-stu-id="662f9-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="662f9-444">Sağlık Kontrollerini Ara Yazılım Kullanma</span><span class="sxs-lookup"><span data-stu-id="662f9-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="662f9-445">Son `Startup.Configure`nokta <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> URL'si veya göreli yolu olan işleme ardışık hattını çağırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="662f9-446">Sistem durumu denetimleri belirli bir bağlantı noktasında dinleyecekse, bağlantı noktasını ayarlamak <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> için aşırı yük kullanın (bağlantı noktası bölümüne göre [Filtre'de](#filter-by-port) daha ayrıntılı olarak açıklanmıştır):</span><span class="sxs-lookup"><span data-stu-id="662f9-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="662f9-447">Sistem durumu denetimi seçenekleri</span><span class="sxs-lookup"><span data-stu-id="662f9-447">Health check options</span></span>

<span data-ttu-id="662f9-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirme kiçin bir fırsat sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="662f9-449">Filtre sağlık denetimleri</span><span class="sxs-lookup"><span data-stu-id="662f9-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="662f9-450">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="662f9-451">Önbellek üstbilgilerini bastırma</span><span class="sxs-lookup"><span data-stu-id="662f9-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="662f9-452">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="662f9-453">Filtre sağlık denetimleri</span><span class="sxs-lookup"><span data-stu-id="662f9-453">Filter health checks</span></span>

<span data-ttu-id="662f9-454">Varsayılan olarak, Sistem Durumu Denetimleri Middleware tüm kayıtlı sistem durumu denetimlerini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="662f9-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="662f9-455">Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> seçene boolean döndüren bir işlev sağlayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="662f9-456">Aşağıdaki örnekte, `Bar` sistem durumu denetimi işlevin koşullu deyimindeki`bar_tag`etiketiyle filtrelenir `true` ve burada yalnızca sağlık denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşirse `foo_tag` veya: `baz_tag`</span><span class="sxs-lookup"><span data-stu-id="662f9-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="662f9-457">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-457">Customize the HTTP status code</span></span>

<span data-ttu-id="662f9-458">Sağlık <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> durumunun eşlemasını HTTP durum kodlarına göre özelleştirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="662f9-459">Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="662f9-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="662f9-460">Gereksinimlerinizi karşılamak için durum kodu değerlerini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="662f9-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="662f9-461">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="662f9-461">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="662f9-462">Önbellek üstbilgilerini bastırma</span><span class="sxs-lookup"><span data-stu-id="662f9-462">Suppress cache headers</span></span>

<span data-ttu-id="662f9-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem Durumu Denetimleri Middleware yanıt önbelleğe önlemek için bir sonda yanıtı http üstleri ekleyip ekledi denetimleri.</span><span class="sxs-lookup"><span data-stu-id="662f9-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="662f9-464">Değer (varsayılan) `false` ise, yanıt önbelleğini önlemek için ara yazılım `Cache-Control`, ve `Expires` `Pragma` üstbilgi kümeler veya üstbilgi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="662f9-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="662f9-465">Değer ise, `true`ara yazılım yanıtın önbellek üstbilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="662f9-466">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="662f9-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="662f9-467">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="662f9-467">Customize output</span></span>

<span data-ttu-id="662f9-468">Seçenek, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> yanıtı yazmak için kullanılan bir temsilcialır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="662f9-469">Varsayılan [temsilci, HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değeriyle en az düz metin yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="662f9-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="662f9-470">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="662f9-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="662f9-471">Varsayılan [temsilci, HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değeriyle en az düz metin yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="662f9-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="662f9-472">Aşağıdaki özel temsilci, `WriteResponse`özel bir JSON yanıtı çıktıları:</span><span class="sxs-lookup"><span data-stu-id="662f9-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="662f9-473">Sistem, karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz, çünkü biçim izleme sistemi seçtiğinize özgüdür.</span><span class="sxs-lookup"><span data-stu-id="662f9-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="662f9-474">İhtiyaçlarınızı karşılamak için `JObject` gerekli olduğu gibi yukarıdaki örnekte özelleştirmek için çekinmeyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="662f9-475">Veritabanı sondası</span><span class="sxs-lookup"><span data-stu-id="662f9-475">Database probe</span></span>

<span data-ttu-id="662f9-476">Sistem durumu denetimi, veritabanının normal yanıt verilip vermeyiş olmadığını belirtmek için boolean testi olarak çalışacak bir veritabanı sorgusu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="662f9-477">Örnek uygulama, bir SQL Server veritabanında sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamaları için bir sistem durumu denetimi kitaplığı olan [AspNetCore.Diagnostics.HealthChecks'i](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır.</span><span class="sxs-lookup"><span data-stu-id="662f9-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="662f9-478">`AspNetCore.Diagnostics.HealthChecks`veritabanına `SELECT 1` bağlantının sağlıklı olduğunu doğrulamak için veritabanına karşı bir sorgu yürütür.</span><span class="sxs-lookup"><span data-stu-id="662f9-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="662f9-479">Bir sorguyla veritabanı bağlantısını denetlerken, hızla dönen bir sorgu seçin.</span><span class="sxs-lookup"><span data-stu-id="662f9-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="662f9-480">Sorgu yaklaşımı veritabanıaşırı yükleme ve performansını düşürme riskini çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="662f9-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="662f9-481">Çoğu durumda, bir test sorgusu çalıştırma gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="662f9-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="662f9-482">Yalnızca veritabanına başarılı bir bağlantı yapmak yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="662f9-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="662f9-483">Bir sorgu çalıştırmak için gerekli bulursanız, gibi `SELECT 1`basit bir SELECT sorgusu seçin.</span><span class="sxs-lookup"><span data-stu-id="662f9-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="662f9-484">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)için bir paket referans ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="662f9-485">Örnek uygulamanın *appsettings.json* dosyasında geçerli bir veritabanı bağlantı dizesi verin.</span><span class="sxs-lookup"><span data-stu-id="662f9-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="662f9-486">Uygulama, aşağıdakiler adlı `HealthCheckSample`bir SQL Server veritabanı kullanır:</span><span class="sxs-lookup"><span data-stu-id="662f9-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="662f9-487">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-488">Örnek uygulama veritabanının bağlantı dizesiyle `AddSqlServer` yöntemi çağırır *(DbHealthStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="662f9-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="662f9-489">Uygulama işleme boru hattında Sağlık Kontrolleri `Startup.Configure`Middleware çağrı:</span><span class="sxs-lookup"><span data-stu-id="662f9-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="662f9-490">Örnek uygulamayı kullanarak veritabanı sondasenaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="662f9-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="662f9-492">Varlık Çerçeve Çekirdek DbContext sondası</span><span class="sxs-lookup"><span data-stu-id="662f9-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="662f9-493">Denetim, `DbContext` uygulamanın BIR EF Core `DbContext`için yapılandırılan veritabanıyla iletişim kurabileceğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="662f9-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="662f9-494">Denetim `DbContext` aşağıdaki uygulamalarda desteklenir:</span><span class="sxs-lookup"><span data-stu-id="662f9-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="662f9-495">[Varlık Çerçevesi (EF) Çekirdeğini](/ef/core/)Kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="662f9-496">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)için bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="662f9-497">`AddDbContextCheck<TContext>`için `DbContext`bir sağlık kontrolü kaydeder.</span><span class="sxs-lookup"><span data-stu-id="662f9-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="662f9-498">Yöntem `DbContext` olarak `TContext` verilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="662f9-499">Hata durumunu, etiketleri ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="662f9-500">Varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="662f9-500">By default:</span></span>

* <span data-ttu-id="662f9-501">EF `DbContextHealthCheck` Core'un `CanConnectAsync` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="662f9-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="662f9-502">Aşırı yükleme yöntemini kullanarak `AddDbContextCheck` sistem durumu denetlerken hangi işlemin çalıştırılabileceğini özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="662f9-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="662f9-503">Sistem durumu denetiminin adı `TContext` türün adıdır.</span><span class="sxs-lookup"><span data-stu-id="662f9-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="662f9-504">Örnek uygulamada, `AppDbContext` *(DbContextHealthStartup.cs)* bir hizmet olarak `Startup.ConfigureServices` sağlanır `AddDbContextCheck` ve bir hizmet olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="662f9-505">Örnek uygulamada, `UseHealthChecks` Sağlık Denetimleri Middleware `Startup.Configure`ekler.</span><span class="sxs-lookup"><span data-stu-id="662f9-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="662f9-506">Örnek uygulamayı `DbContext` kullanarak sonda senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde bulunmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="662f9-507">Veritabanı varsa, silin.</span><span class="sxs-lookup"><span data-stu-id="662f9-507">If the database exists, delete it.</span></span>

<span data-ttu-id="662f9-508">Proje klasöründen komut kabuğunda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="662f9-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="662f9-509">Uygulama çalışmaya devam ettikten sonra, tarayıcıda `/health` bitiş noktasına istekte bulunarak sistem durumunu kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="662f9-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="662f9-510">Veritabanı ve `AppDbContext` yok, bu nedenle uygulama aşağıdaki yanıtı sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="662f9-511">Veritabanını oluşturmak için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="662f9-512">Bir istekte `/createdatabase`bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="662f9-513">Uygulama şu yanıtı verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="662f9-514">`/health` Bitiş noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="662f9-515">Veritabanı ve bağlam vardır, bu nedenle uygulama yanıt verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="662f9-516">Veritabanını silmek için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="662f9-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="662f9-517">Bir istekte `/deletedatabase`bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="662f9-518">Uygulama şu yanıtı verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="662f9-519">`/health` Bitiş noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="662f9-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="662f9-520">Uygulama sağlıksız bir yanıt sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="662f9-521">Ayrı hazırlık ve canlılık sondaları</span><span class="sxs-lookup"><span data-stu-id="662f9-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="662f9-522">Bazı barındırma senaryolarında, iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="662f9-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="662f9-523">Uygulama çalışıyor ama henüz istek almaya hazır değil.</span><span class="sxs-lookup"><span data-stu-id="662f9-523">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="662f9-524">Bu durum uygulamanın *hazır olmasıdır.*</span><span class="sxs-lookup"><span data-stu-id="662f9-524">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="662f9-525">Uygulama çalışıyor ve isteklere yanıt veriyor.</span><span class="sxs-lookup"><span data-stu-id="662f9-525">The app is functioning and responding to requests.</span></span> <span data-ttu-id="662f9-526">Bu durum uygulamanın *canlılığıdır.*</span><span class="sxs-lookup"><span data-stu-id="662f9-526">This state is the app's *liveness*.</span></span>

<span data-ttu-id="662f9-527">Hazırlık denetimi genellikle uygulamanın tüm alt sistemlerinin ve kaynaklarının kullanılabilir olup olmadığını belirlemek için daha kapsamlı ve zaman alan bir denetim ler kümesi gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-527">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="662f9-528">Canlılık denetimi, uygulamanın istekleri işlemek için kullanılabilir olup olmadığını belirlemek için yalnızca hızlı bir denetim gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-528">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="662f9-529">Uygulama hazırlık kontrolünden geçtikten sonra, pahalı hazırlık kontrolleri&mdash;seti ile uygulamayı daha fazla yükmeye gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="662f9-529">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="662f9-530">Örnek uygulama, [Barındırılan Hizmette](xref:fundamentals/host/hosted-services)uzun süren başlangıç görevinin tamamlanamasını bildirmek için bir sistem durumu denetimi içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="662f9-531">Barındırılan `StartupHostedServiceHealthCheck` hizmetin `StartupTaskCompleted`uzun süren görevi tamamlandığında `true` ayarlanabileceği bir özelliği ortaya çıkarır *(StartupHostedServiceHealthCheck.cs):*</span><span class="sxs-lookup"><span data-stu-id="662f9-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="662f9-532">Uzun süren arka plan görevi [Barındırılan Hizmet](xref:fundamentals/host/hosted-services) *(Hizmetler/BaşlangıçHostedService)* tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="662f9-533">Görevin sonunda, `StartupHostedServiceHealthCheck.StartupTaskCompleted` `true`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="662f9-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="662f9-534">Sağlık kontrolü, barındırılan hizmetle <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` birlikte kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="662f9-535">Barındırılan hizmet in sağlık kontrolünde özelliği ayarlamak gerekir, çünkü sağlık kontrolü de hizmet konteyner *(LivenessProbeStartup.cs)* kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="662f9-536">Uygulama işleme ardışık boru hattında `Startup.Configure`Sağlık Denetimleri Middleware'i arayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="662f9-537">Örnek uygulamada, hazır kontrol ve `/health/ready` `/health/live` canlılık denetimi için sağlık denetimi uç noktaları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="662f9-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="662f9-538">Hazır kontrol, sağlık denetimlerini etiketle birlikte `ready` sistem durumu denetimine filtreler.</span><span class="sxs-lookup"><span data-stu-id="662f9-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="662f9-539">Canlılık [denetimi, HealthCheckOptions.Predicate'de](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (daha fazla bilgi için [bkz. Filtre sağlık denetimlerine](#filter-health-checks)bakın) dönerek `StartupHostedServiceHealthCheck` `false` filtreler:</span><span class="sxs-lookup"><span data-stu-id="662f9-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="662f9-540">Örnek uygulamayı kullanarak hazırlık/canlılık yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="662f9-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="662f9-541">Bir tarayıcıda, `/health/ready` 15 saniye geçene kadar birkaç kez ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="662f9-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="662f9-542">Sağlık kontrolü ilk 15 saniye *sağlıksız* bildirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="662f9-543">15 saniye sonra, bitiş noktası barındırılan hizmet tarafından uzun süren görevin tamamlanmasını yansıtan *Sağlıklı*raporlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="662f9-544">Bu örnek, iki saniyelik<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> bir gecikmeyle ilk hazırlık denetimini çalıştıran bir Sağlık Denetimi Yayımcısı (uygulama) da oluşturur.</span><span class="sxs-lookup"><span data-stu-id="662f9-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="662f9-545">Daha fazla bilgi için [Sağlık Denetimi Yayımcısı](#health-check-publisher) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="662f9-546">Kubernetes örneği</span><span class="sxs-lookup"><span data-stu-id="662f9-546">Kubernetes example</span></span>

<span data-ttu-id="662f9-547">Ayrı hazırlık ve canlılık denetimleri kullanarak [Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="662f9-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="662f9-548">Kubernetes'te, bir uygulamanın, temel veritabanı kullanılabilirliğinin sınanması gibi istekleri kabul etmeden önce zaman alan başlangıç çalışması gerçekleştirmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="662f9-549">Ayrı denetimler kullanmak, orkestratörün uygulamanın çalışıp çalışmadığını ancak henüz hazır olup olmadığını veya uygulamanın başlatılıp başlatılamadığını ayırt etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="662f9-550">Kubernetes'teki hazırlık ve canlılık sondaları hakkında daha fazla bilgi için Kubernetes belgelerinde [Canlılık ve Hazırlık Sondalarını Yapılandırıyorum'a](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) bakın.</span><span class="sxs-lookup"><span data-stu-id="662f9-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="662f9-551">Aşağıdaki örnek, Bir Kubernetes hazırlık sondası yapılandırmasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="662f9-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="662f9-552">Özel yanıt yazarı ile metrik tabanlı sonda</span><span class="sxs-lookup"><span data-stu-id="662f9-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="662f9-553">Örnek uygulama, özel bir yanıt yazarıyla bir bellek durumu denetimi gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="662f9-554">`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) daha fazlasını kullanıyorsa sağlıksız bir durum bildirir.</span><span class="sxs-lookup"><span data-stu-id="662f9-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="662f9-555">Uygulama <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> için Çöp Toplayıcı (GC) bilgileri *(MemoryHealthCheck.cs)* içerir:</span><span class="sxs-lookup"><span data-stu-id="662f9-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="662f9-556">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-557">Sağlık kontrolünü sağlayarak etkinleştirmek <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>yerine, `MemoryHealthCheck` hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="662f9-558">Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler sağlık kontrol hizmetleri ve ara yazılımlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="662f9-559">Sağlık kontrolü hizmetlerini Singleton hizmetleri olarak kaydetmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="662f9-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="662f9-560">Örnek uygulamada (*CustomWriterStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="662f9-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="662f9-561">Uygulama işleme ardışık boru hattında `Startup.Configure`Sağlık Denetimleri Middleware'i arayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="662f9-562">Sistem `WriteResponse` durumu denetimi `ResponseWriter` yürütüldüğünde özel bir JSON yanıtı çıktısı için özel bir temsilci verilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="662f9-563">Yöntem, `WriteResponse` json `CompositeHealthCheckResult` nesnesine biçim verir ve sistem durumu denetimi yanıtı için JSON çıktısı verir:</span><span class="sxs-lookup"><span data-stu-id="662f9-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="662f9-564">Örnek uygulamayı kullanarak özel yanıt yazar çıktısı ile metrik tabanlı sondayı çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="662f9-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="662f9-565">[AspNetCore.Diagnostics.HealthChecks,](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) disk depolama ve maksimum değer canlılık denetimleri de dahil olmak üzere metrik tabanlı sistem durumu denetimi senaryoları içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="662f9-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="662f9-567">Bağlantı noktasına göre filtrele</span><span class="sxs-lookup"><span data-stu-id="662f9-567">Filter by port</span></span>

<span data-ttu-id="662f9-568">Bağlantı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> noktasıyla arama yapmak, sistem durumu denetimi isteklerini belirtilen bağlantı noktasına kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="662f9-569">Bu genellikle hizmetleri izlemek için bir bağlantı noktası ortaya çıkarmak için bir kapsayıcı ortamında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="662f9-570">Örnek uygulama, Çevre Değişken [yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="662f9-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="662f9-571">Bağlantı noktası *launchSettings.json* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına aktarılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="662f9-572">Ayrıca, sunucuyu yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="662f9-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="662f9-573">Yönetim bağlantı noktası yapılandırmasını göstermek için örnek uygulamayı kullanmak için, *özellikler* klasöründe *launchSettings.json* dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="662f9-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="662f9-574">Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings.json* dosyası örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulmalıdır:</span><span class="sxs-lookup"><span data-stu-id="662f9-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="662f9-575">Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="662f9-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="662f9-576">Yönetim bağlantı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> noktası *(ManagementPortStartup.cs)* belirtir arama:</span><span class="sxs-lookup"><span data-stu-id="662f9-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="662f9-577">URL'leri ve yönetim bağlantı noktasını açıkça kodda ayarlayarak örnek uygulamada *launchSettings.json* dosyasını oluşturmaktan kaçınabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="662f9-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="662f9-578">Oluşturulduğu *Program.cs,* <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> uygulamanın normal <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> yanıt bitiş noktası ve yönetim bağlantı noktası bitiş noktası için bir çağrı ekleyin ve sağlayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="662f9-579">Çağrıldığı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> *ManagementPortStartup.cs,* yönetim bağlantı noktasını açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="662f9-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="662f9-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="662f9-580">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="662f9-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="662f9-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="662f9-582">Örnek uygulamayı kullanarak yönetim bağlantı noktası yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="662f9-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="662f9-583">Sistem durumu denetimi kitaplığı dağıtma</span><span class="sxs-lookup"><span data-stu-id="662f9-583">Distribute a health check library</span></span>

<span data-ttu-id="662f9-584">Sistem durumu denetimini kitaplık olarak dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="662f9-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="662f9-585"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> Arabirimi bağımsız bir sınıf olarak uygulayan bir sistem durumu denetimi yazın.</span><span class="sxs-lookup"><span data-stu-id="662f9-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="662f9-586">Sınıf [bağımlılık enjeksiyonu (DI),](xref:fundamentals/dependency-injection)tür etkinleştirme ve yapılandırma verilerine erişmek için [adlandırılmış seçeneklere](xref:fundamentals/configuration/options) güvenebilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="662f9-587">Sağlık kontrolleri mantığı: `CheckHealthAsync`</span><span class="sxs-lookup"><span data-stu-id="662f9-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="662f9-588">`data1`ve `data2` sondanın sistem durumu kontrol mantığını çalıştırmak için yöntemde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="662f9-589">`AccessViolationException`işlenir.</span><span class="sxs-lookup"><span data-stu-id="662f9-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="662f9-590">Bir <xref:System.AccessViolationException> durum oluştuğunda, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> kullanıcıların <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> sistem durumu denetimleri hata durumunu yapılandırmasına izin vermek için döndürülür.</span><span class="sxs-lookup"><span data-stu-id="662f9-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="662f9-591">Tüketen uygulamanın yönteminde aradığı parametreleri `Startup.Configure` içeren bir uzantı yöntemi yazın.</span><span class="sxs-lookup"><span data-stu-id="662f9-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="662f9-592">Aşağıdaki örnekte, aşağıdaki sistem durumu denetimi yöntemi imzasını varsayalım:</span><span class="sxs-lookup"><span data-stu-id="662f9-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="662f9-593">Önceki imza, sistem `ExampleHealthCheck` durumu denetimi sondası mantığını işlemek için ek veri gerektirdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="662f9-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="662f9-594">Veriler, sistem durumu denetimi bir uzantı yöntemiyle kaydedildiğinde sistem durumu denetimi örneğini oluşturmak için kullanılan temsilciye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="662f9-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="662f9-595">Aşağıdaki örnekte, arayan isteğe bağlı olarak belirtir:</span><span class="sxs-lookup"><span data-stu-id="662f9-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="662f9-596">sağlık kontrol`name`adı ( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-596">health check name (`name`).</span></span> <span data-ttu-id="662f9-597">Eğer `null` `example_health_check` , kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="662f9-598">sistem durumu denetimi için`data1`dize veri noktası ( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="662f9-599">sistem durumu denetimi için sonda`data2`veri noktası ( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="662f9-600">Eğer `null` `1` , kullanılır.</span><span class="sxs-lookup"><span data-stu-id="662f9-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="662f9-601">hata durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="662f9-602">Varsayılan değer: `null`.</span><span class="sxs-lookup"><span data-stu-id="662f9-602">The default is `null`.</span></span> <span data-ttu-id="662f9-603">If `null`, [HealthStatus.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bir başarısızlık durumu için bildirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="662f9-604">etiketleri`IEnumerable<string>`( ).</span><span class="sxs-lookup"><span data-stu-id="662f9-604">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="662f9-605">Sağlık Kontrolü Yayıncı</span><span class="sxs-lookup"><span data-stu-id="662f9-605">Health Check Publisher</span></span>

<span data-ttu-id="662f9-606">Servis <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> konteynerine bir şey eklendiğinde, sağlık kontrol sistemi sağlık `PublishAsync` kontrollerinizi düzenli aralıklarla yürütür ve sonuçla birlikte çağrıları yerine getirmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="662f9-607">Bu, her işlemin sistem durumunu belirlemek için izleme sistemini periyodik olarak aramasını bekleyen push tabanlı sistem senaryosunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="662f9-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="662f9-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Arabirimin tek bir yöntemi vardır:</span><span class="sxs-lookup"><span data-stu-id="662f9-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="662f9-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>ayarlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="662f9-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="662f9-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Örnekleri yürütmeden önce uygulama başladıktan sonra uygulanan ilk <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> gecikme.</span><span class="sxs-lookup"><span data-stu-id="662f9-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="662f9-611">Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemeler için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="662f9-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="662f9-612">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="662f9-612">The default value is five seconds.</span></span>
* <span data-ttu-id="662f9-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> süresi.</span><span class="sxs-lookup"><span data-stu-id="662f9-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="662f9-614">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="662f9-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="662f9-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> Varsa `null` (varsayılan), sistem durumu denetimi yayımcısı hizmeti tüm kayıtlı sistem durumu denetimlerini çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="662f9-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="662f9-616">Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, denetim kümesini filtreleyen bir işlev sağlayın.</span><span class="sxs-lookup"><span data-stu-id="662f9-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="662f9-617">Yüklem her dönem değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="662f9-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="662f9-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnekler için sistem durumu denetimlerini yürütmek için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="662f9-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="662f9-619">Bir <xref:System.Threading.Timeout.InfiniteTimeSpan> zaman arası olmadan yürütmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="662f9-620">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="662f9-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="662f9-621">ASP.NET Core 2.2 sürümünde, ayar <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulama tarafından onurlandırılan değildir; değerini <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>ayarlar.</span><span class="sxs-lookup"><span data-stu-id="662f9-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="662f9-622">Bu sorun ASP.NET Core 3.0 adresinde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="662f9-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="662f9-623">Örnek uygulamada, `ReadinessPublisher` bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="662f9-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="662f9-624">Sistem durumu denetimi durumu her denetim için aşağıdakilerden biri olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="662f9-625">Bilgi<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) sağlık kontrolleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>durumu ise .</span><span class="sxs-lookup"><span data-stu-id="662f9-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="662f9-626">Hata<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) durum <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ya <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>da .</span><span class="sxs-lookup"><span data-stu-id="662f9-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="662f9-627">Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazırlık denetiminde iki saniyelik bir başlatma gecikmesi vardır ve denetimi her 30 saniyede bir çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="662f9-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="662f9-628"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Uygulamayı etkinleştirmek için, örnek `ReadinessPublisher` bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) konteynerinde tek tonlu bir hizmet olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="662f9-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="662f9-629">Aşağıdaki geçici çözüm, uygulamaya <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> zaten bir veya daha fazla barındırılan hizmet eklendiğinde hizmet kapsayıcısına bir örnek eklenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="662f9-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="662f9-630">Bu geçici çözüm Core 3.0 ASP.NET gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="662f9-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="662f9-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) [Uygulama Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayıncılar içerir.</span><span class="sxs-lookup"><span data-stu-id="662f9-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="662f9-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="662f9-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="662f9-633">MapWhen ile sistem durumu denetimlerini kısıtlama</span><span class="sxs-lookup"><span data-stu-id="662f9-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="662f9-634">Sistem <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> durumu denetimi uç noktaları için istek ardışık dallandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="662f9-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="662f9-635">Aşağıdaki örnekte, `MapWhen` `api/HealthCheck` bitiş noktası için bir GET isteği alınırsa, Sağlık Denetimleri Ara ware etkinleştirmek için istek ardışık dalları:</span><span class="sxs-lookup"><span data-stu-id="662f9-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="662f9-636">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="662f9-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
