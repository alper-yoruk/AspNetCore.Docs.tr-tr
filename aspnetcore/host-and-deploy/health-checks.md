---
title: ASP.NET Core durum denetimleri
author: rick-anderson
description: Uygulamalar ve veritabanları gibi ASP.NET Core altyapısı için sistem durumu denetimlerini ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/health-checks
ms.openlocfilehash: ca5540b4920bc92e968dcbc22a9407453041b01c
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292704"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="241ee-103">ASP.NET Core durum denetimleri</span><span class="sxs-lookup"><span data-stu-id="241ee-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="241ee-104">By [Glenn CONDRON](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="241ee-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="241ee-105">ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu raporlamak için sistem durumu denetimleri ve kitaplıkları sunar.</span><span class="sxs-lookup"><span data-stu-id="241ee-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="241ee-106">Sistem durumu denetimleri, bir uygulama tarafından HTTP uç noktaları olarak gösterilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="241ee-107">Durum denetimi uç noktaları, çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="241ee-108">Sistem durumu araştırmaları, kapsayıcı yöneticileri ve yük dengeleyiciler tarafından bir uygulamanın durumunu denetlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="241ee-109">Örneğin, bir kapsayıcı Orchestrator, sıralı bir dağıtımı kaldırarak veya kapsayıcıyı yeniden başlatarak başarısız olan bir sistem durumu denetimine yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="241ee-110">Yük dengeleyici, trafiği başarısız olan örnekten sağlıklı bir örneğe yönlendirerek sağlıklı olmayan bir uygulamaya tepki verebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="241ee-111">Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı bir durum için izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="241ee-112">Sistem durumu denetimleri, kullanılabilirliği ve normal çalışmayı onaylamak için bir uygulamanın veritabanları ve dış hizmet uç noktaları gibi bağımlılıklarını test edebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="241ee-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="241ee-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="241ee-114">Örnek uygulama, bu konuda açıklanan senaryoların örneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="241ee-115">Örnek uygulamayı belirli bir senaryo için çalıştırmak için, bir komut kabuğunda projenin klasöründen [DotNet Run](/dotnet/core/tools/dotnet-run) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="241ee-116">Örnek uygulamayı kullanma hakkında ayrıntılı bilgi için bu konudaki örnek uygulamanın *README.MD* dosyasına ve senaryo açıklamalarına bakın.</span><span class="sxs-lookup"><span data-stu-id="241ee-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="241ee-117">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="241ee-117">Prerequisites</span></span>

<span data-ttu-id="241ee-118">Durum denetimleri, genellikle bir uygulamanın durumunu denetlemek için bir dış izleme hizmeti veya kapsayıcı Orchestrator ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="241ee-119">Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="241ee-120">İzleme sistemi ne tür bir sistem durumu denetimi oluşturulacağını ve bunların uç noktalarını nasıl yapılandıracağınızı belirler.</span><span class="sxs-lookup"><span data-stu-id="241ee-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="241ee-121">[Microsoft. AspNetCore. Diagnostics. Healthdenetimlerin](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketine ASP.NET Core uygulamaları için örtük olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="241ee-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="241ee-122">Entity Framework Core kullanarak sistem durumu denetimleri gerçekleştirmek için, [Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="241ee-123">Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek üzere başlangıç kodu sağlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="241ee-124">[Veritabanı araştırma](#database-probe) senaryosu, [Aspnetcore. Diagnostics. healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının sistem durumunu denetler.</span><span class="sxs-lookup"><span data-stu-id="241ee-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="241ee-125">[DbContext araştırma](#entity-framework-core-dbcontext-probe) senaryosu bir EF Core kullanarak bir veritabanını denetler `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="241ee-126">Örnek uygulama olan veritabanı senaryolarını araştırmak için:</span><span class="sxs-lookup"><span data-stu-id="241ee-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="241ee-127">Bir veritabanı oluşturur ve *appsettings.js* dosyadaki bağlantı dizesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="241ee-128">, Proje dosyasında aşağıdaki paket başvurularına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="241ee-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="241ee-129">AspNetCore. Healthdenetimleri. SqlServer</span><span class="sxs-lookup"><span data-stu-id="241ee-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="241ee-130">Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="241ee-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="241ee-131">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="241ee-132">Başka bir sistem durumu denetimi senaryosunda, sistem durumu denetimlerinin bir yönetim bağlantı noktasına nasıl filtreleneceği gösterilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="241ee-133">Örnek uygulama, Yönetim URL 'sini ve yönetim bağlantı noktasını içeren dosya *üzerinde bir özellikler/launchSettings.js* oluşturmanızı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="241ee-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="241ee-134">Daha fazla bilgi için, [bağlantı noktasına göre filtrele](#filter-by-port) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="241ee-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="241ee-135">Temel sistem durumu araştırması</span><span class="sxs-lookup"><span data-stu-id="241ee-135">Basic health probe</span></span>

<span data-ttu-id="241ee-136">Birçok uygulama için, uygulamanın istekleri işleme için kullanılabilirliğini raporlayan temel bir durum araştırma yapılandırması, uygulamanın durumunu öğrenmek*liveness*için yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="241ee-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="241ee-137">Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu denetimleri ara yazılımını çağırarak bir URL uç noktasında bir sistem durumu yanıtı ile yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="241ee-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="241ee-138">Varsayılan olarak, belirli bir bağımlılığı veya alt sistemi test etmek için belirli bir sistem durumu denetimi kayıtlı değildir.</span><span class="sxs-lookup"><span data-stu-id="241ee-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="241ee-139">Sistem durumu uç nokta URL 'sinde yanıt veriyorsa, uygulama sağlıklı olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="241ee-140">Varsayılan yanıt yazıcı, durumu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) istemciye geri düz metin yanıtı olarak yazar [. sağlıklı](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)durum, sistem sağlığı durumu [. düşürülmüş](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durum.</span><span class="sxs-lookup"><span data-stu-id="241ee-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="241ee-141">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-142">' İ çağırarak bir sistem durumu denetim uç noktası oluşturun `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="241ee-143">Örnek uygulamada, sistem durumu denetimi uç noktası şurada oluşturulur `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="241ee-144">Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="241ee-145">Docker örneği</span><span class="sxs-lookup"><span data-stu-id="241ee-145">Docker example</span></span>

<span data-ttu-id="241ee-146">[Docker](xref:host-and-deploy/docker/index) `HEALTHCHECK` , temel sistem durumu denetimi yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilen bir yerleşik yönerge sunar:</span><span class="sxs-lookup"><span data-stu-id="241ee-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="241ee-147">Durum denetimleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="241ee-147">Create health checks</span></span>

<span data-ttu-id="241ee-148">Sistem durumu denetimleri, arabirimini uygulayarak oluşturulur <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> .</span><span class="sxs-lookup"><span data-stu-id="241ee-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="241ee-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Yöntemi <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , sistem durumunu, veya olarak belirten bir `Healthy` döndürür `Degraded` `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="241ee-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="241ee-150">Sonuç yapılandırılabilir bir durum kodu ile düz metin yanıtı olarak yazılır (yapılandırma, [sistem durumu denetimi seçenekleri](#health-check-options) bölümünde açıklanmıştır).</span><span class="sxs-lookup"><span data-stu-id="241ee-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="241ee-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, isteğe bağlı anahtar-değer çiftleri de döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="241ee-152">Aşağıdaki `ExampleHealthCheck` sınıf, bir sistem durumu denetiminin yerleşimini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="241ee-153">Durum denetimleri mantığı `CheckHealthAsync` yöntemine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="241ee-154">Aşağıdaki örnek, öğesini olarak bir kukla değişkenini ayarlar `healthCheckResultHealthy` `true` .</span><span class="sxs-lookup"><span data-stu-id="241ee-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="241ee-155">Değeri `healthCheckResultHealthy` olarak ayarlanırsa `false` , [healthcheckresult. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum döndürülür.</span><span class="sxs-lookup"><span data-stu-id="241ee-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="241ee-156">Sistem durumu denetimi hizmetlerini Kaydet</span><span class="sxs-lookup"><span data-stu-id="241ee-156">Register health check services</span></span>

<span data-ttu-id="241ee-157">`ExampleHealthCheck`Türü, içindeki ile sistem durumu denetimi hizmetlerine eklenir <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="241ee-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="241ee-158"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Aşağıdaki örnekte gösterilen aşırı yükleme, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> durum denetimi bir hata bildirdiğinde hata durumu () öğesini raporlamak üzere ayarlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="241ee-159">Hata durumu `null` (varsayılan) olarak ayarlandıysa, [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="241ee-160">Bu aşırı yükleme, kitaplık yazarları için yararlı bir senaryodur. burada, sistem durumu denetimi uygulaması ayarı varsa, bir sistem durumu denetimi hatası oluştuğunda, kitaplık tarafından belirtilen başarısızlık durumu uygulama tarafından zorlanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="241ee-161">*Etiketler* , sistem durumu denetimlerini filtrelemek için kullanılabilir ( [durum denetimleri filtreleme](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).</span><span class="sxs-lookup"><span data-stu-id="241ee-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="241ee-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, bir Lambda işlevi de yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="241ee-163">Aşağıdaki örnekte, sistem durumu denetim adı olarak belirtilir `Example` ve denetim her zaman sağlıklı bir durum döndürür:</span><span class="sxs-lookup"><span data-stu-id="241ee-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="241ee-164"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*>Bağımsız değişkenleri bir sistem durumu denetimi uygulamasına geçirme çağrısı.</span><span class="sxs-lookup"><span data-stu-id="241ee-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="241ee-165">Aşağıdaki örnekte, `TestHealthCheckWithArgs` çağrıldığında bir tamsayıyı ve Use dizesini kabul eder <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> :</span><span class="sxs-lookup"><span data-stu-id="241ee-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="241ee-166">`TestHealthCheckWithArgs`, `AddTypeActivatedCheck` uygulamaya geçirilen tamsayı ve dizeyle çağırarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="241ee-167">Sistem durumu denetimleri yönlendirmeyi kullanma</span><span class="sxs-lookup"><span data-stu-id="241ee-167">Use Health Checks Routing</span></span>

<span data-ttu-id="241ee-168">' De, uç nokta `Startup.Configure` `MapHealthChecks` URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="241ee-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="241ee-169">Konak gerektir</span><span class="sxs-lookup"><span data-stu-id="241ee-169">Require host</span></span>

<span data-ttu-id="241ee-170">`RequireHost`Sistem durumu denetimi uç noktası için izin verilen bir veya daha fazla konağı belirtmek için çağırın.</span><span class="sxs-lookup"><span data-stu-id="241ee-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="241ee-171">Konaklar, puni kodu yerine Unicode olmalıdır ve bir bağlantı noktası içerebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="241ee-172">Bir koleksiyon sağlanmazsa, herhangi bir konak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="241ee-173">Daha fazla bilgi için, [bağlantı noktasına göre filtrele](#filter-by-port) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="241ee-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="241ee-174">Yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="241ee-174">Require authorization</span></span>

<span data-ttu-id="241ee-175">`RequireAuthorization`Sistem durumu denetimi istek uç noktasındaki yetkilendirme ara yazılımını çalıştırma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="241ee-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="241ee-176">`RequireAuthorization`Aşırı yükleme bir veya daha fazla yetkilendirme ilkesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="241ee-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="241ee-177">Bir ilke sağlanmazsa, varsayılan yetkilendirme ilkesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="241ee-178">Kaynaklar Arası İstekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="241ee-179">Bir tarayıcıdan el ile sistem durumu denetimleri gerçekleştirmek yaygın kullanım senaryosu olmasa da, CORS ara yazılımı `RequireCors` sistem durumu denetimleri uç noktaları çağırarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="241ee-180">`RequireCors`Aşırı yükleme BIR CORS ilke Oluşturucu temsilcisini ( `CorsPolicyBuilder` ) veya bir ilke adını kabul eder.</span><span class="sxs-lookup"><span data-stu-id="241ee-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="241ee-181">Bir ilke sağlanmazsa varsayılan CORS ilkesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="241ee-182">Daha fazla bilgi için bkz. <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="241ee-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="241ee-183">Sistem durumu denetimi seçenekleri</span><span class="sxs-lookup"><span data-stu-id="241ee-183">Health check options</span></span>

<span data-ttu-id="241ee-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirmek için bir fırsat sağlayın:</span><span class="sxs-lookup"><span data-stu-id="241ee-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="241ee-185">Durum denetimlerini filtrele</span><span class="sxs-lookup"><span data-stu-id="241ee-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="241ee-186">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="241ee-187">Önbellek üstbilgilerini gösterme</span><span class="sxs-lookup"><span data-stu-id="241ee-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="241ee-188">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="241ee-189">Durum denetimlerini filtrele</span><span class="sxs-lookup"><span data-stu-id="241ee-189">Filter health checks</span></span>

<span data-ttu-id="241ee-190">Varsayılan olarak, sistem durumu denetimleri ara yazılımı tüm kayıtlı sistem durumu denetimlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="241ee-191">Bir sistem durumu denetimleri alt kümesini çalıştırmak için, seçeneğe Boole değeri döndüren bir işlev sağlayın <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> .</span><span class="sxs-lookup"><span data-stu-id="241ee-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="241ee-192">Aşağıdaki örnekte, `Bar` sistem durumu denetimi `bar_tag` işlevin koşullu deyimindeki etiketiyle () tarafından filtrelenir, burada `true` yalnızca sistem durumu denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşiyorsa döndürülür `foo_tag` `baz_tag` .</span><span class="sxs-lookup"><span data-stu-id="241ee-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="241ee-193">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="241ee-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="241ee-194">`Startup.Configure`' De, `Predicate` ' çubuk ' sistem durumu denetimini filtreler.</span><span class="sxs-lookup"><span data-stu-id="241ee-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="241ee-195">Yalnızca Foo ve baz Execute.:</span><span class="sxs-lookup"><span data-stu-id="241ee-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="241ee-196">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-196">Customize the HTTP status code</span></span>

<span data-ttu-id="241ee-197"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>Sistem durumunun http durum kodlarına eşlenmesini özelleştirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="241ee-198">Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="241ee-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="241ee-199">Durum kodu değerlerini gereksinimlerinize uyacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="241ee-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="241ee-200">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="241ee-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="241ee-201">Önbellek üstbilgilerini gösterme</span><span class="sxs-lookup"><span data-stu-id="241ee-201">Suppress cache headers</span></span>

<span data-ttu-id="241ee-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem durumu denetimlerinin, yanıt önbelleğini engellemek için araştırma yanıtına HTTP üstbilgileri ekleyip eklemediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="241ee-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="241ee-203">Değer `false` (varsayılan) ise, ara yazılım, `Cache-Control` `Expires` `Pragma` yanıt önbelleğe almayı engellemek için,, ve üst bilgilerini ayarlar veya geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="241ee-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="241ee-204">Değer ise `true` , ara yazılım yanıtın önbellek üstbilgilerini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="241ee-205">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="241ee-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="241ee-206">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-206">Customize output</span></span>

<span data-ttu-id="241ee-207">İçinde `Startup.Configure` , yanıt yazmak Için [Healthcheckoptions. responsewriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) seçeneğini bir temsilci olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="241ee-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="241ee-208">Varsayılan temsilci, [HealthReport. Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değerine sahip en az bir düz metin yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="241ee-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="241ee-209">Aşağıdaki özel temsilciler özel bir JSON yanıtını çıktı.</span><span class="sxs-lookup"><span data-stu-id="241ee-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="241ee-210">Örnek uygulamadaki ilk örnek, nasıl kullanılacağını göstermektedir <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="241ee-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="241ee-211">İkinci örnek, [üzerindeNewtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/)nasıl kullanacağınızı gösterir:</span><span class="sxs-lookup"><span data-stu-id="241ee-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="241ee-212">Örnek uygulamada, `SYSTEM_TEXT_JSON` sürümünü etkinleştirmek için *CustomWriterStartup.cs* içindeki [Önişlemci yönergesini](xref:index#preprocessor-directives-in-sample-code) not edin `Newtonsoft.Json` `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="241ee-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="241ee-213">Durum denetimleri API 'SI, biçim, izleme sistemine özgü olan karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="241ee-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="241ee-214">Önceki örneklerde gereken yanıtı gerektiği gibi özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="241ee-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="241ee-215">İle JSON serileştirmesi hakkında daha fazla bilgi için `System.Text.Json` bkz. [.net 'te JSON serileştirme ve seri durumdan çıkarma](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="241ee-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="241ee-216">Veritabanı araştırması</span><span class="sxs-lookup"><span data-stu-id="241ee-216">Database probe</span></span>

<span data-ttu-id="241ee-217">Bir sistem durumu denetimi, veritabanının normal olarak yanıt verip vermediğini göstermek üzere Boole testi olarak çalışacak bir veritabanı sorgusu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="241ee-218">Örnek uygulama, SQL Server veritabanında bir sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamalar için bir sistem durumu denetim kitaplığı olan [Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="241ee-219">`AspNetCore.Diagnostics.HealthChecks`veritabanına `SELECT 1` yönelik bağlantının sağlıklı olduğunu doğrulamak için veritabanında bir sorgu yürütür.</span><span class="sxs-lookup"><span data-stu-id="241ee-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="241ee-220">Bir sorgu ile bir veritabanı bağlantısı denetlerken, hızlı bir şekilde dönen bir sorgu seçin.</span><span class="sxs-lookup"><span data-stu-id="241ee-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="241ee-221">Sorgu yaklaşımı, veritabanını aşırı yükleme ve performansını düşürmeye yönelik riski çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="241ee-222">Çoğu durumda, test sorgusunun çalıştırılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="241ee-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="241ee-223">Yalnızca veritabanına başarılı bir bağlantı oluşturmak yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="241ee-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="241ee-224">Bir sorgu çalıştırmak için gerekli olduğunu fark ederseniz, gibi basit bir seçme sorgusu seçin `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="241ee-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="241ee-225">[Aspnetcore. Healthdenetimlerin. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="241ee-226">Örnek uygulamanın dosyasında *appsettings.js* geçerli bir veritabanı bağlantı dizesi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="241ee-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="241ee-227">Uygulama, adında bir SQL Server veritabanı kullanır `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="241ee-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="241ee-228">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-229">Örnek uygulama, `AddSqlServer` yöntemini veritabanının bağlantı dizesiyle (*DbHealthStartup.cs*) çağırır:</span><span class="sxs-lookup"><span data-stu-id="241ee-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="241ee-230">' De çağırarak bir sistem durumu denetim uç noktası oluşturulur `MapHealthChecks` `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="241ee-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="241ee-231">Örnek uygulamayı kullanarak veritabanı araştırma senaryosunu çalıştırmak için, bir komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="241ee-232">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="241ee-233">DbContext araştırması Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="241ee-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="241ee-234">`DbContext`Denetim, uygulamanın bir EF Core için yapılandırılmış veritabanıyla iletişim kurabildiğini onaylar `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="241ee-235">`DbContext`Denetim şu uygulamalar için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="241ee-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="241ee-236">[Entity Framework (EF) Core](/ef/core/)kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="241ee-237">[Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)'a bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="241ee-238">`AddDbContextCheck<TContext>`için bir sistem durumu denetimi kaydeder `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="241ee-239">, `DbContext` Yöntemi olarak olarak sağlanır `TContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="241ee-240">Hata durumu, Etiketler ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="241ee-241">Varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="241ee-241">By default:</span></span>

* <span data-ttu-id="241ee-242">`DbContextHealthCheck`EF Core `CanConnectAsync` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="241ee-243">Yöntem aşırı yüklerini kullanarak sistem durumunu denetlerken hangi işlemin çalıştırılacağını özelleştirebilirsiniz `AddDbContextCheck` .</span><span class="sxs-lookup"><span data-stu-id="241ee-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="241ee-244">Sistem durumu denetiminin adı, `TContext` türün adıdır.</span><span class="sxs-lookup"><span data-stu-id="241ee-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="241ee-245">Örnek uygulamada, `AppDbContext` `AddDbContextCheck` içinde bir hizmet olarak sağlanır ve `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="241ee-246">' De çağırarak bir sistem durumu denetim uç noktası oluşturulur `MapHealthChecks` `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="241ee-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="241ee-247">`DbContext`Örnek uygulamayı kullanarak araştırma senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde mevcut olmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="241ee-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="241ee-248">Veritabanı varsa, silin.</span><span class="sxs-lookup"><span data-stu-id="241ee-248">If the database exists, delete it.</span></span>

<span data-ttu-id="241ee-249">Komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="241ee-250">Uygulama çalıştıktan sonra, bir tarayıcıda uç noktaya istek yaparak sistem durumunu kontrol edin `/health` .</span><span class="sxs-lookup"><span data-stu-id="241ee-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="241ee-251">Veritabanı ve `AppDbContext` yok, uygulama aşağıdaki yanıtı sağlar:</span><span class="sxs-lookup"><span data-stu-id="241ee-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="241ee-252">Veritabanını oluşturmak için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="241ee-253">İçin bir istek yapın `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="241ee-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="241ee-254">Uygulama yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="241ee-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="241ee-255">Uç noktaya bir istek oluşturun `/health` .</span><span class="sxs-lookup"><span data-stu-id="241ee-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="241ee-256">Veritabanı ve bağlam var, bu nedenle uygulama yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="241ee-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="241ee-257">Veritabanını silmek için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="241ee-258">İçin bir istek yapın `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="241ee-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="241ee-259">Uygulama yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="241ee-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="241ee-260">Uç noktaya bir istek oluşturun `/health` .</span><span class="sxs-lookup"><span data-stu-id="241ee-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="241ee-261">Uygulama sağlıksız bir yanıt sağlar:</span><span class="sxs-lookup"><span data-stu-id="241ee-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="241ee-262">Ayrı hazırlık ve lizlilik araştırmaları</span><span class="sxs-lookup"><span data-stu-id="241ee-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="241ee-263">Bazı barındırma senaryolarında iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="241ee-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="241ee-264">*Hazırlık* , uygulamanın normal çalışıp çalışmadığını ancak istekleri almaya hazır olmadığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="241ee-265">*Lida* bir uygulamanın kilitlenip yeniden başlatılması gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="241ee-266">Aşağıdaki örneği göz önünde bulundurun: bir uygulamanın istekleri işlemeye hazırlanmadan önce büyük bir yapılandırma dosyasını indirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="241ee-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="241ee-267">Uygulamanın dosyayı birkaç kez indirmeyi yeniden denemesini sağladığından, ilk indirme işlemi başarısız olursa uygulamanın yeniden başlatılmasını istemiyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="241ee-268">İşlemin desteklerini betimleyen, ek denetimler gerçekleştirilmeyen bir *araştırma* kullanıyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="241ee-269">Yapılandırma dosyası indirmesi başarılı olmadan önce isteklerin uygulamaya gönderilmesini de engellemek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="241ee-270">İndirme başarılı olana ve uygulama istekleri almaya hazır olana kadar "hazır değil" durumunu göstermek için bir *hazırlık araştırması* kullanıyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="241ee-271">Örnek uygulama, [barındırılan bir hizmette](xref:fundamentals/host/hosted-services)uzun süre çalışan başlatma görevinin tamamlandığını raporlamak için bir sistem durumu denetimi içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="241ee-272">, `StartupHostedServiceHealthCheck` `StartupTaskCompleted` Barındırılan hizmetin uzun süre çalışan görevi bittiğinde olarak ayarlayabilmesini sağlayan bir özelliği sunar `true` (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="241ee-273">Uzun süre çalışan arka plan görevi bir [barındırılan hizmet](xref:fundamentals/host/hosted-services) (*Hizmetler/startuphostedservice*) tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="241ee-274">Görevin sonunda şu `StartupHostedServiceHealthCheck.StartupTaskCompleted` şekilde ayarlanır `true` :</span><span class="sxs-lookup"><span data-stu-id="241ee-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="241ee-275">Sistem durumu denetimi <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` barındırılan hizmetle birlikte ' de kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="241ee-276">Barındırılan hizmetin, sistem durumu denetiminde özelliği ayarlaması gerektiğinden, sistem durumu denetimi de hizmet kapsayıcısına kaydedilir (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="241ee-277">' De çağırarak bir sistem durumu denetim uç noktası oluşturulur `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="241ee-278">Örnek uygulamada, sistem durumu denetimi uç noktaları şu konumda oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="241ee-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="241ee-279">`/health/ready`Hazırlık denetimi için.</span><span class="sxs-lookup"><span data-stu-id="241ee-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="241ee-280">Hazır olma durumu denetimi, sistem durumu denetimine, etiketiyle sistem durumunu denetler `ready` .</span><span class="sxs-lookup"><span data-stu-id="241ee-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="241ee-281">`/health/live`göz atın.</span><span class="sxs-lookup"><span data-stu-id="241ee-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="241ee-282">Libu denetim, `StartupHostedServiceHealthCheck` `false` [Healthcheckoptions. koşula](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) dönerek tarafından filtreleneceğini denetler (daha fazla bilgi için bkz. [filtre durumu denetimleri](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="241ee-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="241ee-283">Aşağıdaki örnek kodda:</span><span class="sxs-lookup"><span data-stu-id="241ee-283">In the following example code:</span></span>

* <span data-ttu-id="241ee-284">Hazır olma denetimi ' hazır ' etiketiyle tüm kayıtlı denetimleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="241ee-285">, `Predicate` Tüm denetimleri dışlar ve 200-Tamam döndürür.</span><span class="sxs-lookup"><span data-stu-id="241ee-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="241ee-286">Örnek uygulamayı kullanarak hazırlık/lizlilik yapılandırma senaryosunu çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="241ee-287">Bir tarayıcıda, `/health/ready` 15 saniye geçtikten sonra birkaç kez ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="241ee-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="241ee-288">Sistem durumu denetimi ilk 15 saniye boyunca *sağlıksız* durum bildiriyor.</span><span class="sxs-lookup"><span data-stu-id="241ee-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="241ee-289">15 saniye sonra, uç nokta, barındırılan hizmet tarafından uzun süre çalışan görevin tamamlandığını yansıtan *sağlıklı*şekilde raporlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="241ee-290">Bu örnek ayrıca <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> , iki saniyelik bir gecikmeyle ilk hazırlık denetimini çalıştıran bir sistem durumu denetimi yayımcısı (uygulama) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="241ee-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="241ee-291">Daha fazla bilgi için bkz. [sistem durumu denetimi yayımcısı](#health-check-publisher) bölümü.</span><span class="sxs-lookup"><span data-stu-id="241ee-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="241ee-292">Kubernetes örneği</span><span class="sxs-lookup"><span data-stu-id="241ee-292">Kubernetes example</span></span>

<span data-ttu-id="241ee-293">Ayrı hazır olma ve [Ezlilik denetimleri kullanmak, Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="241ee-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="241ee-294">Kubernetes 'te, temel veritabanı kullanılabilirliğinin bir testi gibi istekleri kabul etmeden önce, zaman alan Başlangıç işlerini gerçekleştirmek için bir uygulamanın olması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="241ee-295">Ayrı denetimlerin kullanılması, Orchestrator 'ın uygulamanın çalışıp çalışmadığını, ancak henüz hazırlanmadığını ya da uygulamanın başlayamadığını ayırt etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="241ee-296">Kubernetes 'te hazır olma ve Elanma araştırmaları hakkında daha fazla bilgi için bkz. Kubernetes belgelerindeki [limize ve hazırlık araştırmalarını yapılandırma](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) .</span><span class="sxs-lookup"><span data-stu-id="241ee-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="241ee-297">Aşağıdaki örnekte bir Kubernetes Readiness araştırma yapılandırması gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="241ee-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="241ee-298">Özel bir yanıt yazıcı ile ölçüm tabanlı araştırma</span><span class="sxs-lookup"><span data-stu-id="241ee-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="241ee-299">Örnek uygulama, özel bir yanıt yazıcısı ile bir bellek durumu denetimini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="241ee-300">`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) kullanıyorsa, düzeyi düşürülmüş bir durum bildirir.</span><span class="sxs-lookup"><span data-stu-id="241ee-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="241ee-301">, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Uygulama Için çöp toplayıcı (GC) bilgilerini içerir (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="241ee-302">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-303">Durum denetimini öğesine geçirerek etkinleştirmek yerine, <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="241ee-304">Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler, sistem durumu denetimi Hizmetleri ve ara yazılım tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="241ee-305">Sistem durumu denetimi hizmetlerini tek hizmet olarak kaydetmeyi öneririz.</span><span class="sxs-lookup"><span data-stu-id="241ee-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="241ee-306">Örnek uygulamanın *CustomWriterStartup.cs* ' de:</span><span class="sxs-lookup"><span data-stu-id="241ee-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="241ee-307">' De çağırarak bir sistem durumu denetim uç noktası oluşturulur `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="241ee-308">`WriteResponse`Sistem durumu denetimi yürütüldüğünde özel bır JSON yanıtının çıkışı Için Microsoft. AspNetCore. Diagnostics. Healthdenetimleri. HealthCheckOptions. ResponseWriter> özelliği <bir temsilci sağlanır:</span><span class="sxs-lookup"><span data-stu-id="241ee-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="241ee-309">`WriteResponse`Temsilci, `CompositeHealthCheckResult` bir JSON nesnesi halinde biçimlendirir ve sistem durumu denetimi yanıtı için JSON çıktısı verir.</span><span class="sxs-lookup"><span data-stu-id="241ee-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="241ee-310">Daha fazla bilgi için [çıktıyı özelleştirme](#customize-output) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="241ee-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="241ee-311">Örnek uygulamayı kullanarak özel yanıt yazıcı çıkışıyla ölçüm tabanlı araştırmayı çalıştırmak için, komut kabuğu 'ndaki projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="241ee-312">[Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , disk depolama ve maksimum değer düzeyi denetimleri dahil ölçüm tabanlı sistem durumu denetimi senaryolarını içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="241ee-313">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="241ee-314">Bağlantı noktasına göre filtrele</span><span class="sxs-lookup"><span data-stu-id="241ee-314">Filter by port</span></span>

<span data-ttu-id="241ee-315">`RequireHost` `MapHealthChecks` Belirtilen bağlantı noktasına sistem durumu denetim isteklerini kısıtlamak için bir bağlantı noktası BELIRTEN bir URL düzeniyle üzerinde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="241ee-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="241ee-316">Bu genellikle bir kapsayıcı ortamında, izleme hizmetleri için bir bağlantı noktası oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="241ee-317">Örnek uygulama, [ortam değişkeni yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables)kullanarak bağlantı noktasını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="241ee-318">Bağlantı noktası *launchSettings.js* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına geçirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="241ee-319">Ayrıca, sunucuyu Yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="241ee-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="241ee-320">Yönetim bağlantı noktası yapılandırmasını göstermek üzere örnek uygulamayı kullanmak için bir *Özellikler* klasöründe *launchSettings.js* dosya oluşturun.</span><span class="sxs-lookup"><span data-stu-id="241ee-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="241ee-321">Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings.js* , örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulması gerekir:</span><span class="sxs-lookup"><span data-stu-id="241ee-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="241ee-322">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-323">' İ çağırarak bir sistem durumu denetim uç noktası oluşturun `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="241ee-324">Örnek uygulamada, içindeki uç noktada öğesine yapılan bir çağrı, `RequireHost` `Startup.Configure` yapılandırmadan yönetim bağlantı noktasını belirtir:</span><span class="sxs-lookup"><span data-stu-id="241ee-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="241ee-325">Uç noktalar içindeki örnek uygulamada oluşturulur `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="241ee-326">Aşağıdaki örnek kodda:</span><span class="sxs-lookup"><span data-stu-id="241ee-326">In the following example code:</span></span>

* <span data-ttu-id="241ee-327">Hazır olma denetimi ' hazır ' etiketiyle tüm kayıtlı denetimleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="241ee-328">, `Predicate` Tüm denetimleri dışlar ve 200-Tamam döndürür.</span><span class="sxs-lookup"><span data-stu-id="241ee-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="241ee-329">Yönetim bağlantı noktasını kodda açıkça ayarlayarak örnek uygulamada *launchSettings.js* dosya oluşturmaktan kaçınabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="241ee-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="241ee-330">Uygulamasının oluşturulduğu *program.cs* içinde, <xref:Microsoft.Extensions.Hosting.HostBuilder> için bir çağrı ekleyin <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> ve uygulamanın yönetim bağlantı noktası uç noktasını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="241ee-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="241ee-331">`Configure` *ManagementPortStartup.cs*' de, ile yönetim bağlantı noktasını belirtin `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="241ee-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="241ee-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="241ee-332">*Program.cs*:</span></span>
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
> <span data-ttu-id="241ee-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="241ee-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="241ee-334">Yönetim bağlantı noktası yapılandırma senaryosunu örnek uygulamayı kullanarak çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="241ee-335">Bir sistem durumu denetim kitaplığı dağıtma</span><span class="sxs-lookup"><span data-stu-id="241ee-335">Distribute a health check library</span></span>

<span data-ttu-id="241ee-336">Bir sistem durumu denetimini kitaplık olarak dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="241ee-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="241ee-337">Arabirimi tek başına sınıf olarak uygulayan bir sistem durumu denetimi yazın <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> .</span><span class="sxs-lookup"><span data-stu-id="241ee-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="241ee-338">Sınıf, yapılandırma verilerine erişmek için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection), tür etkinleştirme ve [adlandırılmış seçenekleri](xref:fundamentals/configuration/options) kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="241ee-339">Sistem durumu denetimleri mantığı `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="241ee-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="241ee-340">`data1`ve `data2` araştırma sistem durumu denetimi mantığını çalıştırmak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="241ee-341">`AccessViolationException`işlenir.</span><span class="sxs-lookup"><span data-stu-id="241ee-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="241ee-342">Bir <xref:System.AccessViolationException> gerçekleştiğinde, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> kullanıcıların sistem durumu denetimleri hata durumunu yapılandırmasına izin vermek için ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="241ee-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="241ee-343">Kullanan uygulamanın yöntemi içinde çağırdığı parametrelere sahip bir genişletme yöntemi yazın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="241ee-344">Aşağıdaki örnekte, aşağıdaki sistem durumu denetim yöntemi imzasını varsayın:</span><span class="sxs-lookup"><span data-stu-id="241ee-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="241ee-345">Yukarıdaki imza, `ExampleHealthCheck` durumunun sistem durumu denetimi araştırma mantığını işlemek için ek veriler gerektirdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="241ee-346">Veriler, sistem durumu denetimi bir genişletme yöntemiyle kaydedildiğinde sistem durumu denetim örneğini oluşturmak için kullanılan temsilciye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="241ee-347">Aşağıdaki örnekte, çağıran isteğe bağlı olarak şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="241ee-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="241ee-348">sistem durumu denetim adı ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="241ee-348">health check name (`name`).</span></span> <span data-ttu-id="241ee-349">İse `null` , `example_health_check` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="241ee-350">sistem durumu denetimi () için dize veri noktası `data1` .</span><span class="sxs-lookup"><span data-stu-id="241ee-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="241ee-351">sistem durumu denetimi () için tamsayı veri noktası `data2` .</span><span class="sxs-lookup"><span data-stu-id="241ee-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="241ee-352">İse `null` , `1` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="241ee-353">hata durumu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="241ee-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="241ee-354">Varsayılan değer: `null`.</span><span class="sxs-lookup"><span data-stu-id="241ee-354">The default is `null`.</span></span> <span data-ttu-id="241ee-355">`null`Bir hata durumu için [HealthStatus. sağlıksız durum](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="241ee-356">Etiketler ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="241ee-356">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="241ee-357">Sistem durumu denetimi yayımcısı</span><span class="sxs-lookup"><span data-stu-id="241ee-357">Health Check Publisher</span></span>

<span data-ttu-id="241ee-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Hizmet kapsayıcısına eklendiğinde, sistem durumu denetimi sistemi düzenli olarak sistem durumu denetim ve çağrılarınızı yürütülür `PublishAsync` .</span><span class="sxs-lookup"><span data-stu-id="241ee-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="241ee-359">Bu, her bir işlemin sistem durumunu belirlemede düzenli aralıklarla izleme sistemini çağırmasını bekleyen, gönderim tabanlı bir sistem durumu izleme sistemi senaryosunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="241ee-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="241ee-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Arabirim tek bir yönteme sahiptir:</span><span class="sxs-lookup"><span data-stu-id="241ee-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="241ee-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>şunları ayarlamanıza izin verir:</span><span class="sxs-lookup"><span data-stu-id="241ee-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="241ee-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Örnekleri yürütmeden önce uygulama başladıktan sonra uygulanacak ilk gecikme <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> .</span><span class="sxs-lookup"><span data-stu-id="241ee-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="241ee-363">Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemelere uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="241ee-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="241ee-364">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="241ee-364">The default value is five seconds.</span></span>
* <span data-ttu-id="241ee-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Yürütme süresi.</span><span class="sxs-lookup"><span data-stu-id="241ee-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="241ee-366">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="241ee-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="241ee-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (Varsayılan) ise, sistem durumu denetimi yayımcı hizmeti tüm kayıtlı sistem durumu denetimlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="241ee-368">Bir sistem durumu denetimleri alt kümesini çalıştırmak için denetim kümesini filtreleyen bir işlev sağlayın.</span><span class="sxs-lookup"><span data-stu-id="241ee-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="241ee-369">Koşul her dönem değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="241ee-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Tüm örnekler için sistem durumu denetimlerini yürütmeye yönelik zaman aşımı <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> .</span><span class="sxs-lookup"><span data-stu-id="241ee-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="241ee-371"><xref:System.Threading.Timeout.InfiniteTimeSpan>Zaman aşımı olmadan yürütmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="241ee-372">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="241ee-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="241ee-373">Örnek uygulamada, bir uygulamadır `ReadinessPublisher` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> .</span><span class="sxs-lookup"><span data-stu-id="241ee-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="241ee-374">Sistem durumu denetimi durumu her denetim için günlük düzeyinde günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="241ee-375"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>Durum denetim durumu ise, bilgi () <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="241ee-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="241ee-376"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>Durum ya da ise hata () <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="241ee-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="241ee-377">Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazır olma denetimi iki saniyelik başlangıç gecikmesine sahiptir ve denetimi her 30 saniyede bir çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="241ee-378">Uygulamayı etkinleştirmek için <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnek, `ReadinessPublisher` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında tek bir hizmet olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="241ee-379">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , [Application Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayımcılar içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="241ee-380">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="241ee-381">Durum denetimlerini Mapperne zaman kısıtla</span><span class="sxs-lookup"><span data-stu-id="241ee-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="241ee-382"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>Durum denetimi uç noktaları için istek ardışık düzenini koşullu olarak dallandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="241ee-383">Aşağıdaki örnekte, `MapWhen` uç nokta için BIR get isteği alındığında durum denetimleri ara yazılımını etkinleştirmek üzere istek ardışık düzenini dallandırır `api/HealthCheck` :</span><span class="sxs-lookup"><span data-stu-id="241ee-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="241ee-384">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span><span class="sxs-lookup"><span data-stu-id="241ee-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="241ee-385">ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu raporlamak için sistem durumu denetimleri ve kitaplıkları sunar.</span><span class="sxs-lookup"><span data-stu-id="241ee-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="241ee-386">Sistem durumu denetimleri, bir uygulama tarafından HTTP uç noktaları olarak gösterilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="241ee-387">Durum denetimi uç noktaları, çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="241ee-388">Sistem durumu araştırmaları, kapsayıcı yöneticileri ve yük dengeleyiciler tarafından bir uygulamanın durumunu denetlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="241ee-389">Örneğin, bir kapsayıcı Orchestrator, sıralı bir dağıtımı kaldırarak veya kapsayıcıyı yeniden başlatarak başarısız olan bir sistem durumu denetimine yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="241ee-390">Yük dengeleyici, trafiği başarısız olan örnekten sağlıklı bir örneğe yönlendirerek sağlıklı olmayan bir uygulamaya tepki verebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="241ee-391">Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı bir durum için izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="241ee-392">Sistem durumu denetimleri, kullanılabilirliği ve normal çalışmayı onaylamak için bir uygulamanın veritabanları ve dış hizmet uç noktaları gibi bağımlılıklarını test edebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="241ee-393">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="241ee-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="241ee-394">Örnek uygulama, bu konuda açıklanan senaryoların örneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="241ee-395">Örnek uygulamayı belirli bir senaryo için çalıştırmak için, bir komut kabuğunda projenin klasöründen [DotNet Run](/dotnet/core/tools/dotnet-run) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="241ee-396">Örnek uygulamayı kullanma hakkında ayrıntılı bilgi için bu konudaki örnek uygulamanın *README.MD* dosyasına ve senaryo açıklamalarına bakın.</span><span class="sxs-lookup"><span data-stu-id="241ee-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="241ee-397">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="241ee-397">Prerequisites</span></span>

<span data-ttu-id="241ee-398">Durum denetimleri, genellikle bir uygulamanın durumunu denetlemek için bir dış izleme hizmeti veya kapsayıcı Orchestrator ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="241ee-399">Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="241ee-400">İzleme sistemi ne tür bir sistem durumu denetimi oluşturulacağını ve bunların uç noktalarını nasıl yapılandıracağınızı belirler.</span><span class="sxs-lookup"><span data-stu-id="241ee-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="241ee-401">Microsoft. aspnetcore [. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Aspnetcore. Diagnostics. healthdenetimlerin](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="241ee-402">Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek üzere başlangıç kodu sağlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="241ee-403">[Veritabanı araştırma](#database-probe) senaryosu, [Aspnetcore. Diagnostics. healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının sistem durumunu denetler.</span><span class="sxs-lookup"><span data-stu-id="241ee-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="241ee-404">[DbContext araştırma](#entity-framework-core-dbcontext-probe) senaryosu bir EF Core kullanarak bir veritabanını denetler `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="241ee-405">Örnek uygulama olan veritabanı senaryolarını araştırmak için:</span><span class="sxs-lookup"><span data-stu-id="241ee-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="241ee-406">Bir veritabanı oluşturur ve *appsettings.js* dosyadaki bağlantı dizesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="241ee-407">, Proje dosyasında aşağıdaki paket başvurularına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="241ee-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="241ee-408">AspNetCore. Healthdenetimleri. SqlServer</span><span class="sxs-lookup"><span data-stu-id="241ee-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="241ee-409">Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="241ee-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="241ee-410">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="241ee-411">Başka bir sistem durumu denetimi senaryosunda, sistem durumu denetimlerinin bir yönetim bağlantı noktasına nasıl filtreleneceği gösterilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="241ee-412">Örnek uygulama, Yönetim URL 'sini ve yönetim bağlantı noktasını içeren dosya *üzerinde bir özellikler/launchSettings.js* oluşturmanızı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="241ee-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="241ee-413">Daha fazla bilgi için, [bağlantı noktasına göre filtrele](#filter-by-port) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="241ee-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="241ee-414">Temel sistem durumu araştırması</span><span class="sxs-lookup"><span data-stu-id="241ee-414">Basic health probe</span></span>

<span data-ttu-id="241ee-415">Birçok uygulama için, uygulamanın istekleri işleme için kullanılabilirliğini raporlayan temel bir durum araştırma yapılandırması, uygulamanın durumunu öğrenmek*liveness*için yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="241ee-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="241ee-416">Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu denetimleri ara yazılımını çağırarak bir URL uç noktasında bir sistem durumu yanıtı ile yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="241ee-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="241ee-417">Varsayılan olarak, belirli bir bağımlılığı veya alt sistemi test etmek için belirli bir sistem durumu denetimi kayıtlı değildir.</span><span class="sxs-lookup"><span data-stu-id="241ee-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="241ee-418">Sistem durumu uç nokta URL 'sinde yanıt veriyorsa, uygulama sağlıklı olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="241ee-419">Varsayılan yanıt yazıcı, durumu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) istemciye geri düz metin yanıtı olarak yazar [. sağlıklı](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)durum, sistem sağlığı durumu [. düşürülmüş](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durum.</span><span class="sxs-lookup"><span data-stu-id="241ee-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="241ee-420">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-421">İstek işleme ardışık düzeninde bulunan sistem durumu denetimleri ara yazılımı için bir uç nokta ekleyin <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="241ee-422">Örnek uygulamada, sistem durumu denetimi uç noktası şurada oluşturulur `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="241ee-423">Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="241ee-424">Docker örneği</span><span class="sxs-lookup"><span data-stu-id="241ee-424">Docker example</span></span>

<span data-ttu-id="241ee-425">[Docker](xref:host-and-deploy/docker/index) `HEALTHCHECK` , temel sistem durumu denetimi yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilen bir yerleşik yönerge sunar:</span><span class="sxs-lookup"><span data-stu-id="241ee-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="241ee-426">Durum denetimleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="241ee-426">Create health checks</span></span>

<span data-ttu-id="241ee-427">Sistem durumu denetimleri, arabirimini uygulayarak oluşturulur <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> .</span><span class="sxs-lookup"><span data-stu-id="241ee-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="241ee-428"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Yöntemi <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , sistem durumunu, veya olarak belirten bir `Healthy` döndürür `Degraded` `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="241ee-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="241ee-429">Sonuç yapılandırılabilir bir durum kodu ile düz metin yanıtı olarak yazılır (yapılandırma, [sistem durumu denetimi seçenekleri](#health-check-options) bölümünde açıklanmıştır).</span><span class="sxs-lookup"><span data-stu-id="241ee-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="241ee-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, isteğe bağlı anahtar-değer çiftleri de döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="241ee-431">Örnek sistem durumu denetimi</span><span class="sxs-lookup"><span data-stu-id="241ee-431">Example health check</span></span>

<span data-ttu-id="241ee-432">Aşağıdaki `ExampleHealthCheck` sınıf, bir sistem durumu denetiminin yerleşimini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="241ee-433">Durum denetimleri mantığı `CheckHealthAsync` yöntemine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="241ee-434">Aşağıdaki örnek, öğesini olarak bir kukla değişkenini ayarlar `healthCheckResultHealthy` `true` .</span><span class="sxs-lookup"><span data-stu-id="241ee-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="241ee-435">Değeri `healthCheckResultHealthy` olarak ayarlanırsa `false` , [healthcheckresult. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum döndürülür.</span><span class="sxs-lookup"><span data-stu-id="241ee-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="241ee-436">Sistem durumu denetimi hizmetlerini Kaydet</span><span class="sxs-lookup"><span data-stu-id="241ee-436">Register health check services</span></span>

<span data-ttu-id="241ee-437">`ExampleHealthCheck`Türü, ile içindeki sistem durumu denetimi hizmetlerine eklenir `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="241ee-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="241ee-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Aşağıdaki örnekte gösterilen aşırı yükleme, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> durum denetimi bir hata bildirdiğinde hata durumu () öğesini raporlamak üzere ayarlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="241ee-439">Hata durumu `null` (varsayılan) olarak ayarlandıysa, [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="241ee-440">Bu aşırı yükleme, kitaplık yazarları için yararlı bir senaryodur. burada, sistem durumu denetimi uygulaması ayarı varsa, bir sistem durumu denetimi hatası oluştuğunda, kitaplık tarafından belirtilen başarısızlık durumu uygulama tarafından zorlanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="241ee-441">*Etiketler* , sistem durumu denetimlerini filtrelemek için kullanılabilir ( [durum denetimleri filtreleme](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).</span><span class="sxs-lookup"><span data-stu-id="241ee-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="241ee-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, bir Lambda işlevi de yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="241ee-443">Aşağıdaki `Startup.ConfigureServices` örnekte, sistem durumu denetim adı olarak belirtilir `Example` ve denetim her zaman sağlıklı bir durum döndürür:</span><span class="sxs-lookup"><span data-stu-id="241ee-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="241ee-444">Sistem durumu denetimleri ara yazılımı kullan</span><span class="sxs-lookup"><span data-stu-id="241ee-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="241ee-445">İçinde `Startup.Configure` , <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> işlem ardışık düzeninde uç nokta URL 'si veya göreli yol ile çağrı yapın:</span><span class="sxs-lookup"><span data-stu-id="241ee-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="241ee-446">Durum denetimlerinin belirli bir bağlantı noktasını dinlemesi gerekiyorsa, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> bağlantı noktasını ayarlamak için bir aşırı yüklemesi kullanın ( [bağlantı noktasına göre filtrele](#filter-by-port) bölümünde anlatılmıştır):</span><span class="sxs-lookup"><span data-stu-id="241ee-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="241ee-447">Sistem durumu denetimi seçenekleri</span><span class="sxs-lookup"><span data-stu-id="241ee-447">Health check options</span></span>

<span data-ttu-id="241ee-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirmek için bir fırsat sağlayın:</span><span class="sxs-lookup"><span data-stu-id="241ee-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="241ee-449">Durum denetimlerini filtrele</span><span class="sxs-lookup"><span data-stu-id="241ee-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="241ee-450">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="241ee-451">Önbellek üstbilgilerini gösterme</span><span class="sxs-lookup"><span data-stu-id="241ee-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="241ee-452">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="241ee-453">Durum denetimlerini filtrele</span><span class="sxs-lookup"><span data-stu-id="241ee-453">Filter health checks</span></span>

<span data-ttu-id="241ee-454">Varsayılan olarak, sistem durumu denetimleri ara yazılımı tüm kayıtlı sistem durumu denetimlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="241ee-455">Bir sistem durumu denetimleri alt kümesini çalıştırmak için, seçeneğe Boole değeri döndüren bir işlev sağlayın <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> .</span><span class="sxs-lookup"><span data-stu-id="241ee-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="241ee-456">Aşağıdaki örnekte, `Bar` sistem durumu denetimi `bar_tag` işlevin koşullu deyimindeki etiketiyle () tarafından filtrelenir, burada `true` yalnızca sistem durumu denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşiyorsa döndürülür `foo_tag` `baz_tag` .</span><span class="sxs-lookup"><span data-stu-id="241ee-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="241ee-457">HTTP durum kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-457">Customize the HTTP status code</span></span>

<span data-ttu-id="241ee-458"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>Sistem durumunun http durum kodlarına eşlenmesini özelleştirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="241ee-459">Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="241ee-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="241ee-460">Durum kodu değerlerini gereksinimlerinize uyacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="241ee-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="241ee-461">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="241ee-461">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="241ee-462">Önbellek üstbilgilerini gösterme</span><span class="sxs-lookup"><span data-stu-id="241ee-462">Suppress cache headers</span></span>

<span data-ttu-id="241ee-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem durumu denetimlerinin, yanıt önbelleğini engellemek için araştırma yanıtına HTTP üstbilgileri ekleyip eklemediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="241ee-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="241ee-464">Değer `false` (varsayılan) ise, ara yazılım, `Cache-Control` `Expires` `Pragma` yanıt önbelleğe almayı engellemek için,, ve üst bilgilerini ayarlar veya geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="241ee-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="241ee-465">Değer ise `true` , ara yazılım yanıtın önbellek üstbilgilerini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="241ee-466">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="241ee-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="241ee-467">Çıktıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="241ee-467">Customize output</span></span>

<span data-ttu-id="241ee-468"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter>Seçeneği, yanıtı yazmak için kullanılan bir temsilciyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="241ee-469">Varsayılan temsilci, [HealthReport. Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değerine sahip en az bir düz metin yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="241ee-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="241ee-470">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="241ee-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="241ee-471">Varsayılan temsilci, [HealthReport. Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değerine sahip en az bir düz metin yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="241ee-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="241ee-472">Aşağıdaki özel temsilci, `WriteResponse` Özel BIR JSON yanıtı verir:</span><span class="sxs-lookup"><span data-stu-id="241ee-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="241ee-473">Durum denetimleri sistemi, karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz çünkü biçim, sizin tercih ettiğiniz izleme sistemine özgüdür.</span><span class="sxs-lookup"><span data-stu-id="241ee-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="241ee-474">`JObject`Gereksinimlerinizi karşılamak için gereken önceki örnekteki ' i özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="241ee-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="241ee-475">Veritabanı araştırması</span><span class="sxs-lookup"><span data-stu-id="241ee-475">Database probe</span></span>

<span data-ttu-id="241ee-476">Bir sistem durumu denetimi, veritabanının normal olarak yanıt verip vermediğini göstermek üzere Boole testi olarak çalışacak bir veritabanı sorgusu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="241ee-477">Örnek uygulama, SQL Server veritabanında bir sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamalar için bir sistem durumu denetim kitaplığı olan [Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="241ee-478">`AspNetCore.Diagnostics.HealthChecks`veritabanına `SELECT 1` yönelik bağlantının sağlıklı olduğunu doğrulamak için veritabanında bir sorgu yürütür.</span><span class="sxs-lookup"><span data-stu-id="241ee-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="241ee-479">Bir sorgu ile bir veritabanı bağlantısı denetlerken, hızlı bir şekilde dönen bir sorgu seçin.</span><span class="sxs-lookup"><span data-stu-id="241ee-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="241ee-480">Sorgu yaklaşımı, veritabanını aşırı yükleme ve performansını düşürmeye yönelik riski çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="241ee-481">Çoğu durumda, test sorgusunun çalıştırılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="241ee-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="241ee-482">Yalnızca veritabanına başarılı bir bağlantı oluşturmak yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="241ee-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="241ee-483">Bir sorgu çalıştırmak için gerekli olduğunu fark ederseniz, gibi basit bir seçme sorgusu seçin `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="241ee-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="241ee-484">[Aspnetcore. Healthdenetimlerin. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="241ee-485">Örnek uygulamanın dosyasında *appsettings.js* geçerli bir veritabanı bağlantı dizesi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="241ee-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="241ee-486">Uygulama, adında bir SQL Server veritabanı kullanır `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="241ee-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="241ee-487">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-488">Örnek uygulama, `AddSqlServer` yöntemini veritabanının bağlantı dizesiyle (*DbHealthStartup.cs*) çağırır:</span><span class="sxs-lookup"><span data-stu-id="241ee-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="241ee-489">İçindeki uygulama işleme ardışık düzeninde bulunan sistem durumu denetimleri ara yazılımı `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="241ee-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="241ee-490">Örnek uygulamayı kullanarak veritabanı araştırma senaryosunu çalıştırmak için, bir komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="241ee-491">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="241ee-492">DbContext araştırması Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="241ee-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="241ee-493">`DbContext`Denetim, uygulamanın bir EF Core için yapılandırılmış veritabanıyla iletişim kurabildiğini onaylar `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="241ee-494">`DbContext`Denetim şu uygulamalar için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="241ee-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="241ee-495">[Entity Framework (EF) Core](/ef/core/)kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="241ee-496">[Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)'a bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="241ee-497">`AddDbContextCheck<TContext>`için bir sistem durumu denetimi kaydeder `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="241ee-498">, `DbContext` Yöntemi olarak olarak sağlanır `TContext` .</span><span class="sxs-lookup"><span data-stu-id="241ee-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="241ee-499">Hata durumu, Etiketler ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="241ee-500">Varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="241ee-500">By default:</span></span>

* <span data-ttu-id="241ee-501">`DbContextHealthCheck`EF Core `CanConnectAsync` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="241ee-502">Yöntem aşırı yüklerini kullanarak sistem durumunu denetlerken hangi işlemin çalıştırılacağını özelleştirebilirsiniz `AddDbContextCheck` .</span><span class="sxs-lookup"><span data-stu-id="241ee-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="241ee-503">Sistem durumu denetiminin adı, `TContext` türün adıdır.</span><span class="sxs-lookup"><span data-stu-id="241ee-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="241ee-504">Örnek uygulamada, `AppDbContext` `AddDbContextCheck` içinde bir hizmet olarak sağlanır ve `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="241ee-505">Örnek uygulamada, `UseHealthChecks` Içindeki sistem durumu denetimleri ara yazılımını ekler `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="241ee-506">`DbContext`Örnek uygulamayı kullanarak araştırma senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde mevcut olmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="241ee-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="241ee-507">Veritabanı varsa, silin.</span><span class="sxs-lookup"><span data-stu-id="241ee-507">If the database exists, delete it.</span></span>

<span data-ttu-id="241ee-508">Komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="241ee-509">Uygulama çalıştıktan sonra, bir tarayıcıda uç noktaya istek yaparak sistem durumunu kontrol edin `/health` .</span><span class="sxs-lookup"><span data-stu-id="241ee-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="241ee-510">Veritabanı ve `AppDbContext` yok, uygulama aşağıdaki yanıtı sağlar:</span><span class="sxs-lookup"><span data-stu-id="241ee-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="241ee-511">Veritabanını oluşturmak için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="241ee-512">İçin bir istek yapın `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="241ee-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="241ee-513">Uygulama yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="241ee-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="241ee-514">Uç noktaya bir istek oluşturun `/health` .</span><span class="sxs-lookup"><span data-stu-id="241ee-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="241ee-515">Veritabanı ve bağlam var, bu nedenle uygulama yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="241ee-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="241ee-516">Veritabanını silmek için örnek uygulamayı tetikleyin.</span><span class="sxs-lookup"><span data-stu-id="241ee-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="241ee-517">İçin bir istek yapın `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="241ee-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="241ee-518">Uygulama yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="241ee-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="241ee-519">Uç noktaya bir istek oluşturun `/health` .</span><span class="sxs-lookup"><span data-stu-id="241ee-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="241ee-520">Uygulama sağlıksız bir yanıt sağlar:</span><span class="sxs-lookup"><span data-stu-id="241ee-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="241ee-521">Ayrı hazırlık ve lizlilik araştırmaları</span><span class="sxs-lookup"><span data-stu-id="241ee-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="241ee-522">Bazı barındırma senaryolarında iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="241ee-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="241ee-523">*Hazırlık* , uygulamanın normal çalışıp çalışmadığını ancak istekleri almaya hazır olmadığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-523">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="241ee-524">*Lida* bir uygulamanın kilitlenip yeniden başlatılması gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-524">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="241ee-525">Aşağıdaki örneği göz önünde bulundurun: bir uygulamanın istekleri işlemeye hazırlanmadan önce büyük bir yapılandırma dosyasını indirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="241ee-525">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="241ee-526">Uygulamanın dosyayı birkaç kez indirmeyi yeniden denemesini sağladığından, ilk indirme işlemi başarısız olursa uygulamanın yeniden başlatılmasını istemiyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-526">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="241ee-527">İşlemin desteklerini betimleyen, ek denetimler gerçekleştirilmeyen bir *araştırma* kullanıyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-527">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="241ee-528">Yapılandırma dosyası indirmesi başarılı olmadan önce isteklerin uygulamaya gönderilmesini de engellemek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-528">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="241ee-529">İndirme başarılı olana ve uygulama istekleri almaya hazır olana kadar "hazır değil" durumunu göstermek için bir *hazırlık araştırması* kullanıyoruz.</span><span class="sxs-lookup"><span data-stu-id="241ee-529">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="241ee-530">Örnek uygulama, [barındırılan bir hizmette](xref:fundamentals/host/hosted-services)uzun süre çalışan başlatma görevinin tamamlandığını raporlamak için bir sistem durumu denetimi içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="241ee-531">, `StartupHostedServiceHealthCheck` `StartupTaskCompleted` Barındırılan hizmetin uzun süre çalışan görevi bittiğinde olarak ayarlayabilmesini sağlayan bir özelliği sunar `true` (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="241ee-532">Uzun süre çalışan arka plan görevi bir [barındırılan hizmet](xref:fundamentals/host/hosted-services) (*Hizmetler/startuphostedservice*) tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="241ee-533">Görevin sonunda şu `StartupHostedServiceHealthCheck.StartupTaskCompleted` şekilde ayarlanır `true` :</span><span class="sxs-lookup"><span data-stu-id="241ee-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="241ee-534">Sistem durumu denetimi <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` barındırılan hizmetle birlikte ' de kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="241ee-535">Barındırılan hizmetin, sistem durumu denetiminde özelliği ayarlaması gerektiğinden, sistem durumu denetimi de hizmet kapsayıcısına kaydedilir (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="241ee-536">İçindeki uygulama işleme ardışık düzeninde sistem durumu denetimleri ara yazılımı `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="241ee-537">Örnek uygulamada, sistem durumu denetimi uç noktaları, `/health/ready` hazırlık denetimi ve `/health/live` Liya denetimi için konumunda oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="241ee-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="241ee-538">Hazır olma durumu denetimi, sistem durumu denetimine, etiketiyle sistem durumunu denetler `ready` .</span><span class="sxs-lookup"><span data-stu-id="241ee-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="241ee-539">Libu denetim, `StartupHostedServiceHealthCheck` `false` [Healthcheckoptions. koşula](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) dönerek tarafından filtreleneceğini denetler (daha fazla bilgi için bkz. [sistem durumu denetimlerini filtreleme](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="241ee-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="241ee-540">Örnek uygulamayı kullanarak hazırlık/lizlilik yapılandırma senaryosunu çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="241ee-541">Bir tarayıcıda, `/health/ready` 15 saniye geçtikten sonra birkaç kez ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="241ee-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="241ee-542">Sistem durumu denetimi ilk 15 saniye boyunca *sağlıksız* durum bildiriyor.</span><span class="sxs-lookup"><span data-stu-id="241ee-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="241ee-543">15 saniye sonra, uç nokta, barındırılan hizmet tarafından uzun süre çalışan görevin tamamlandığını yansıtan *sağlıklı*şekilde raporlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="241ee-544">Bu örnek ayrıca <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> , iki saniyelik bir gecikmeyle ilk hazırlık denetimini çalıştıran bir sistem durumu denetimi yayımcısı (uygulama) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="241ee-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="241ee-545">Daha fazla bilgi için bkz. [sistem durumu denetimi yayımcısı](#health-check-publisher) bölümü.</span><span class="sxs-lookup"><span data-stu-id="241ee-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="241ee-546">Kubernetes örneği</span><span class="sxs-lookup"><span data-stu-id="241ee-546">Kubernetes example</span></span>

<span data-ttu-id="241ee-547">Ayrı hazır olma ve [Ezlilik denetimleri kullanmak, Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="241ee-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="241ee-548">Kubernetes 'te, temel veritabanı kullanılabilirliğinin bir testi gibi istekleri kabul etmeden önce, zaman alan Başlangıç işlerini gerçekleştirmek için bir uygulamanın olması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="241ee-549">Ayrı denetimlerin kullanılması, Orchestrator 'ın uygulamanın çalışıp çalışmadığını, ancak henüz hazırlanmadığını ya da uygulamanın başlayamadığını ayırt etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="241ee-550">Kubernetes 'te hazır olma ve Elanma araştırmaları hakkında daha fazla bilgi için bkz. Kubernetes belgelerindeki [limize ve hazırlık araştırmalarını yapılandırma](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) .</span><span class="sxs-lookup"><span data-stu-id="241ee-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="241ee-551">Aşağıdaki örnekte bir Kubernetes Readiness araştırma yapılandırması gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="241ee-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="241ee-552">Özel bir yanıt yazıcı ile ölçüm tabanlı araştırma</span><span class="sxs-lookup"><span data-stu-id="241ee-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="241ee-553">Örnek uygulama, özel bir yanıt yazıcısı ile bir bellek durumu denetimini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="241ee-554">`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) kullanıyorsa sağlıksız bir durum bildirir.</span><span class="sxs-lookup"><span data-stu-id="241ee-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="241ee-555">, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Uygulama Için çöp toplayıcı (GC) bilgilerini içerir (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="241ee-556">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-557">Durum denetimini öğesine geçirerek etkinleştirmek yerine, <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="241ee-558">Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler, sistem durumu denetimi Hizmetleri ve ara yazılım tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="241ee-559">Sistem durumu denetimi hizmetlerini tek hizmet olarak kaydetmeyi öneririz.</span><span class="sxs-lookup"><span data-stu-id="241ee-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="241ee-560">Örnek uygulamada (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="241ee-561">İçindeki uygulama işleme ardışık düzeninde sistem durumu denetimleri ara yazılımı `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="241ee-562">`WriteResponse` `ResponseWriter` Durum denetimi yürütüldüğünde özel bir JSON yanıtının çıkışı için özelliğine bir temsilci sağlanır:</span><span class="sxs-lookup"><span data-stu-id="241ee-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="241ee-563">`WriteResponse`Yöntemi `CompositeHealthCheckResult` bir JSON nesnesi olarak biçimlendirir ve sistem durumu denetimi yanıtı için JSON çıktısı verir:</span><span class="sxs-lookup"><span data-stu-id="241ee-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="241ee-564">Örnek uygulamayı kullanarak özel yanıt yazıcı çıkışıyla ölçüm tabanlı araştırmayı çalıştırmak için, komut kabuğu 'ndaki projenin klasöründen aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="241ee-565">[Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , disk depolama ve maksimum değer düzeyi denetimleri dahil ölçüm tabanlı sistem durumu denetimi senaryolarını içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="241ee-566">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="241ee-567">Bağlantı noktasına göre filtrele</span><span class="sxs-lookup"><span data-stu-id="241ee-567">Filter by port</span></span>

<span data-ttu-id="241ee-568"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>Bir bağlantı noktası ile çağırmak, sistem durumu denetimi isteklerini belirtilen bağlantı noktasına kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="241ee-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="241ee-569">Bu genellikle bir kapsayıcı ortamında, izleme hizmetleri için bir bağlantı noktası oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="241ee-570">Örnek uygulama, [ortam değişkeni yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="241ee-571">Bağlantı noktası *launchSettings.js* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına geçirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="241ee-572">Ayrıca, sunucuyu Yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="241ee-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="241ee-573">Yönetim bağlantı noktası yapılandırmasını göstermek üzere örnek uygulamayı kullanmak için bir *Özellikler* klasöründe *launchSettings.js* dosya oluşturun.</span><span class="sxs-lookup"><span data-stu-id="241ee-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="241ee-574">Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings.js* , örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulması gerekir:</span><span class="sxs-lookup"><span data-stu-id="241ee-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="241ee-575">İle sistem durumu denetimi hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="241ee-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="241ee-576">İçin yapılan çağrı, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Yönetim bağlantı noktasını belirtir (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="241ee-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="241ee-577">Kod içinde açıkça URL 'Ler ve yönetim bağlantı noktası ayarlayarak örnek uygulamada *launchSettings.js* oluşturmaktan kaçınabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="241ee-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="241ee-578">*Program.cs* içinde oluşturulduğu yerde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> , için bir çağrı ekleyin <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> ve uygulamanın normal yanıt uç noktasını ve yönetim bağlantı noktası uç noktasını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="241ee-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="241ee-579">*ManagementPortStartup.cs* burada <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> çağrıldığında, yönetim bağlantı noktasını açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="241ee-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="241ee-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="241ee-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="241ee-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="241ee-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="241ee-582">Yönetim bağlantı noktası yapılandırma senaryosunu örnek uygulamayı kullanarak çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:</span><span class="sxs-lookup"><span data-stu-id="241ee-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="241ee-583">Bir sistem durumu denetim kitaplığı dağıtma</span><span class="sxs-lookup"><span data-stu-id="241ee-583">Distribute a health check library</span></span>

<span data-ttu-id="241ee-584">Bir sistem durumu denetimini kitaplık olarak dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="241ee-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="241ee-585">Arabirimi tek başına sınıf olarak uygulayan bir sistem durumu denetimi yazın <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> .</span><span class="sxs-lookup"><span data-stu-id="241ee-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="241ee-586">Sınıf, yapılandırma verilerine erişmek için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection), tür etkinleştirme ve [adlandırılmış seçenekleri](xref:fundamentals/configuration/options) kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="241ee-587">Sistem durumu denetimleri mantığı `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="241ee-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="241ee-588">`data1`ve `data2` araştırma sistem durumu denetimi mantığını çalıştırmak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="241ee-589">`AccessViolationException`işlenir.</span><span class="sxs-lookup"><span data-stu-id="241ee-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="241ee-590">Bir <xref:System.AccessViolationException> gerçekleştiğinde, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> kullanıcıların sistem durumu denetimleri hata durumunu yapılandırmasına izin vermek için ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="241ee-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="241ee-591">Kullanan uygulamanın yöntemi içinde çağırdığı parametrelere sahip bir genişletme yöntemi yazın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="241ee-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="241ee-592">Aşağıdaki örnekte, aşağıdaki sistem durumu denetim yöntemi imzasını varsayın:</span><span class="sxs-lookup"><span data-stu-id="241ee-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="241ee-593">Yukarıdaki imza, `ExampleHealthCheck` durumunun sistem durumu denetimi araştırma mantığını işlemek için ek veriler gerektirdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="241ee-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="241ee-594">Veriler, sistem durumu denetimi bir genişletme yöntemiyle kaydedildiğinde sistem durumu denetim örneğini oluşturmak için kullanılan temsilciye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="241ee-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="241ee-595">Aşağıdaki örnekte, çağıran isteğe bağlı olarak şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="241ee-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="241ee-596">sistem durumu denetim adı ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="241ee-596">health check name (`name`).</span></span> <span data-ttu-id="241ee-597">İse `null` , `example_health_check` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="241ee-598">sistem durumu denetimi () için dize veri noktası `data1` .</span><span class="sxs-lookup"><span data-stu-id="241ee-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="241ee-599">sistem durumu denetimi () için tamsayı veri noktası `data2` .</span><span class="sxs-lookup"><span data-stu-id="241ee-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="241ee-600">İse `null` , `1` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="241ee-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="241ee-601">hata durumu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="241ee-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="241ee-602">Varsayılan değer: `null`.</span><span class="sxs-lookup"><span data-stu-id="241ee-602">The default is `null`.</span></span> <span data-ttu-id="241ee-603">`null`Bir hata durumu için [HealthStatus. sağlıksız durum](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="241ee-604">Etiketler ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="241ee-604">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="241ee-605">Sistem durumu denetimi yayımcısı</span><span class="sxs-lookup"><span data-stu-id="241ee-605">Health Check Publisher</span></span>

<span data-ttu-id="241ee-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Hizmet kapsayıcısına eklendiğinde, sistem durumu denetimi sistemi düzenli olarak sistem durumu denetim ve çağrılarınızı yürütülür `PublishAsync` .</span><span class="sxs-lookup"><span data-stu-id="241ee-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="241ee-607">Bu, her bir işlemin sistem durumunu belirlemede düzenli aralıklarla izleme sistemini çağırmasını bekleyen, gönderim tabanlı bir sistem durumu izleme sistemi senaryosunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="241ee-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="241ee-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Arabirim tek bir yönteme sahiptir:</span><span class="sxs-lookup"><span data-stu-id="241ee-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="241ee-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>şunları ayarlamanıza izin verir:</span><span class="sxs-lookup"><span data-stu-id="241ee-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="241ee-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Örnekleri yürütmeden önce uygulama başladıktan sonra uygulanacak ilk gecikme <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> .</span><span class="sxs-lookup"><span data-stu-id="241ee-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="241ee-611">Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemelere uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="241ee-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="241ee-612">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="241ee-612">The default value is five seconds.</span></span>
* <span data-ttu-id="241ee-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Yürütme süresi.</span><span class="sxs-lookup"><span data-stu-id="241ee-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="241ee-614">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="241ee-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="241ee-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (Varsayılan) ise, sistem durumu denetimi yayımcı hizmeti tüm kayıtlı sistem durumu denetimlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="241ee-616">Bir sistem durumu denetimleri alt kümesini çalıştırmak için denetim kümesini filtreleyen bir işlev sağlayın.</span><span class="sxs-lookup"><span data-stu-id="241ee-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="241ee-617">Koşul her dönem değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="241ee-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="241ee-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Tüm örnekler için sistem durumu denetimlerini yürütmeye yönelik zaman aşımı <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> .</span><span class="sxs-lookup"><span data-stu-id="241ee-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="241ee-619"><xref:System.Threading.Timeout.InfiniteTimeSpan>Zaman aşımı olmadan yürütmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="241ee-620">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="241ee-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="241ee-621">ASP.NET Core 2,2 sürümünde, ayar <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> uygulama tarafından kabul edilemez <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ; değerini ayarlar <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> .</span><span class="sxs-lookup"><span data-stu-id="241ee-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="241ee-622">Bu sorun ASP.NET Core 3,0 ' de giderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="241ee-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="241ee-623">Örnek uygulamada, bir uygulamadır `ReadinessPublisher` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> .</span><span class="sxs-lookup"><span data-stu-id="241ee-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="241ee-624">Durum denetimi durumu her denetim için şu şekilde günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="241ee-625"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>Durum denetim durumu ise, bilgi () <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="241ee-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="241ee-626"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>Durum ya da ise hata () <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="241ee-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="241ee-627">Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazır olma denetimi iki saniyelik başlangıç gecikmesine sahiptir ve denetimi her 30 saniyede bir çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="241ee-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="241ee-628">Uygulamayı etkinleştirmek için <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnek, `ReadinessPublisher` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında tek bir hizmet olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="241ee-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="241ee-629">Aşağıdaki geçici çözüm, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamaya bir veya daha fazla barındırılan hizmet zaten eklenmiş olduğunda hizmet kapsayıcısına bir örnek eklenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="241ee-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="241ee-630">Bu geçici çözüm ASP.NET Core 3,0 ' de gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="241ee-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="241ee-631">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , [Application Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayımcılar içerir.</span><span class="sxs-lookup"><span data-stu-id="241ee-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="241ee-632">[Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="241ee-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="241ee-633">Durum denetimlerini Mapperne zaman kısıtla</span><span class="sxs-lookup"><span data-stu-id="241ee-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="241ee-634"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>Durum denetimi uç noktaları için istek ardışık düzenini koşullu olarak dallandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="241ee-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="241ee-635">Aşağıdaki örnekte, `MapWhen` uç nokta için BIR get isteği alındığında durum denetimleri ara yazılımını etkinleştirmek üzere istek ardışık düzenini dallandırır `api/HealthCheck` :</span><span class="sxs-lookup"><span data-stu-id="241ee-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="241ee-636">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="241ee-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
