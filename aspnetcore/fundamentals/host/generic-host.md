---
title: .NET genel ana bilgisayar
author: rick-anderson
description: Uygulama başlatma ve ömür yönetiminden sorumlu .NET Core genel ana bilgisayarı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 268c507ee35c9c0432c3dd2da2a389308531b9f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775602"
---
# <a name="net-generic-host"></a><span data-ttu-id="e35b4-103">.NET genel ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="e35b4-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="e35b4-104">ASP.NET Core şablonları bir .NET Core genel ana bilgisayarı oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="e35b4-105">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="e35b4-105">Host definition</span></span>

<span data-ttu-id="e35b4-106">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e35b4-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e35b4-107">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="e35b4-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="e35b4-108">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="e35b4-108">Logging</span></span>
* <span data-ttu-id="e35b4-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e35b4-109">Configuration</span></span>
* <span data-ttu-id="e35b4-110">`IHostedService`uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="e35b4-110">`IHostedService` implementations</span></span>

<span data-ttu-id="e35b4-111">Bir konak başlatıldığında, bu uygulamanın, `IHostedService.StartAsync` dı kapsayıcısında bulduğu her <xref:Microsoft.Extensions.Hosting.IHostedService> bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="e35b4-112">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e35b4-113">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="e35b4-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e35b4-114">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="e35b4-114">Set up a host</span></span>

<span data-ttu-id="e35b4-115">Ana bilgisayar genellikle `Program` sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e35b4-116">`Main` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e35b4-116">The `Main` method:</span></span>

* <span data-ttu-id="e35b4-117">Bir Oluşturucu `CreateHostBuilder` nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e35b4-118">Oluşturucu `Build` nesnesindeki `Run` çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e35b4-119">ASP.NET Core Web şablonları, genel bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e35b4-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="e35b4-120">Aşağıdaki kod, HTTP olmayan iş yükünü kullanan genel bir konak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="e35b4-121">`IHostedService` Uygulama, dı kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-121">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="e35b4-122">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak `CreateHostBuilder` çağrılır: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="e35b4-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e35b4-123">Yukarıdaki kod ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="e35b4-124">Uygulama Entity Framework Core kullanıyorsa, `CreateHostBuilder` yöntemin adını veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="e35b4-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e35b4-125">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , uygulamayı çalıştırmadan Konağı yapılandıran `CreateHostBuilder` bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e35b4-126">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="e35b4-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e35b4-127">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="e35b4-127">Default builder settings</span></span>

<span data-ttu-id="e35b4-128"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e35b4-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="e35b4-129">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından <xref:System.IO.Directory.GetCurrentDirectory*>döndürülen yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="e35b4-130">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="e35b4-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="e35b4-131">Ön eki olan `DOTNET_`ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e35b4-132">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-132">Command-line arguments.</span></span>
* <span data-ttu-id="e35b4-133">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="e35b4-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="e35b4-134">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e35b4-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="e35b4-135">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e35b4-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e35b4-136">[Secret Manager](xref:security/app-secrets) Uygulama `Development` ortamda çalıştığında gizli dizi Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="e35b4-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e35b4-137">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-137">Environment variables.</span></span>
  * <span data-ttu-id="e35b4-138">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-138">Command-line arguments.</span></span>
* <span data-ttu-id="e35b4-139">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="e35b4-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e35b4-140">Konsol</span><span class="sxs-lookup"><span data-stu-id="e35b4-140">Console</span></span>
  * <span data-ttu-id="e35b4-141">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="e35b4-141">Debug</span></span>
  * <span data-ttu-id="e35b4-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="e35b4-142">EventSource</span></span>
  * <span data-ttu-id="e35b4-143">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="e35b4-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e35b4-144">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e35b4-145">`ConfigureWebHostDefaults` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e35b4-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="e35b4-146">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e35b4-147">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e35b4-148">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options>..</span><span class="sxs-lookup"><span data-stu-id="e35b4-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="e35b4-149">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="e35b4-150">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` Eşitse `true`, [iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e35b4-151">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-151">Enables IIS integration.</span></span> <span data-ttu-id="e35b4-152">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options>..</span><span class="sxs-lookup"><span data-stu-id="e35b4-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e35b4-153">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e35b4-154">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="e35b4-154">Framework-provided services</span></span>

<span data-ttu-id="e35b4-155">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e35b4-156">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e35b4-157">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e35b4-158">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="e35b4-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e35b4-159">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services>..</span><span class="sxs-lookup"><span data-stu-id="e35b4-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e35b4-160">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="e35b4-161">Başlatma sonrası <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> ve düzgün `IApplicationLifetime`kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e35b4-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e35b4-162">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e35b4-163">Arabirim Ayrıca bir `StopApplication` yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e35b4-164">Aşağıdaki örnek, olayları kaydeden `IHostedService` `IHostApplicationLifetime` bir uygulamasıdır:</span><span class="sxs-lookup"><span data-stu-id="e35b4-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e35b4-165">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-165">IHostLifetime</span></span>

<span data-ttu-id="e35b4-166"><xref:Microsoft.Extensions.Hosting.IHostLifetime> Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e35b4-167">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-167">The last implementation registered is used.</span></span>

<span data-ttu-id="e35b4-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="e35b4-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e35b4-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e35b4-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e35b4-170"><kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm 'i dinler ve kapalı <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="e35b4-171">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e35b4-172">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="e35b4-172">IHostEnvironment</span></span>

<span data-ttu-id="e35b4-173">Aşağıdaki ayarlarla <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e35b4-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e35b4-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e35b4-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e35b4-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e35b4-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e35b4-176">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="e35b4-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="e35b4-177">Web Apps, `IWebHostEnvironment` [WebRootPath](#webroot)öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="e35b4-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e35b4-178">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e35b4-178">Host configuration</span></span>

<span data-ttu-id="e35b4-179">Ana bilgisayar yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e35b4-180">Konak yapılandırması, içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [Hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="e35b4-181">Sonra `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e35b4-182">Konak yapılandırması eklemek için üzerinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e35b4-183">`ConfigureHostConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e35b4-184">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e35b4-185">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından `CreateDefaultBuilder`dahildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e35b4-186">Web Apps için, ön ekine `ASPNETCORE_` sahip ortam değişkeni sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e35b4-187">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e35b4-188">Örneğin, için `ASPNETCORE_ENVIRONMENT` ortam değişkeni değeri, `environment` anahtar için ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e35b4-189">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e35b4-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e35b4-190">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e35b4-190">App configuration</span></span>

<span data-ttu-id="e35b4-191">Uygulama yapılandırması, üzerinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`çağırarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e35b4-192">`ConfigureAppConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e35b4-193">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e35b4-194">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma, sonraki Işlemler ve dı hizmeti olarak, [hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) konumunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e35b4-195">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e35b4-196">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e35b4-197">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="e35b4-197">Settings for all app types</span></span>

<span data-ttu-id="e35b4-198">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e35b4-199">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e35b4-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e35b4-200">ApplicationName</span></span>

<span data-ttu-id="e35b4-201">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e35b4-202">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="e35b4-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e35b4-203">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-203">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-204">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="e35b4-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e35b4-205">**Ortam değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e35b4-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e35b4-206">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="e35b4-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-207">ContentRoot</span></span>

<span data-ttu-id="e35b4-208">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e35b4-209">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e35b4-210">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e35b4-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e35b4-211">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-211">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-212">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="e35b4-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e35b4-213">**Ortam değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e35b4-214">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseContentRoot` `IHostBuilder`arama yapın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e35b4-215">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-215">For more information, see:</span></span>

* [<span data-ttu-id="e35b4-216">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="e35b4-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e35b4-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e35b4-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e35b4-218">EnvironmentName</span></span>

<span data-ttu-id="e35b4-219">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e35b4-220">Çerçeve tanımlı değerler, `Development` `Staging`, ve `Production`içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e35b4-221">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e35b4-222">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="e35b4-222">**Key**: `environment`</span></span>  
<span data-ttu-id="e35b4-223">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-223">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-224">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="e35b4-224">**Default**: `Production`</span></span>  
<span data-ttu-id="e35b4-225">**Ortam değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e35b4-226">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseEnvironment` `IHostBuilder`arama yapın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e35b4-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e35b4-227">ShutdownTimeout</span></span>

<span data-ttu-id="e35b4-228">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman aşımını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e35b4-229">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-229">The default value is five seconds.</span></span>  <span data-ttu-id="e35b4-230">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="e35b4-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="e35b4-231">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e35b4-232">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e35b4-233">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e35b4-234">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e35b4-235">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e35b4-236">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e35b4-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e35b4-237">**Şunu yazın**:`int`</span><span class="sxs-lookup"><span data-stu-id="e35b4-237">**Type**: `int`</span></span>  
<span data-ttu-id="e35b4-238">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="e35b4-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e35b4-239">**Ortam değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e35b4-240">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e35b4-241">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="e35b4-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="e35b4-242">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="e35b4-242">Settings for web apps</span></span>

<span data-ttu-id="e35b4-243">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e35b4-244">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e35b4-245">Üzerinde `IWebHostBuilder` uzantı yöntemleri bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e35b4-246">Aşağıdaki örnekte olduğu gibi, ' nin `webBuilder` `IWebHostBuilder`bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="e35b4-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e35b4-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e35b4-247">CaptureStartupErrors</span></span>

<span data-ttu-id="e35b4-248">Ne `false`zaman, başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="e35b4-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e35b4-249">Ne `true`zaman, ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e35b4-250">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e35b4-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e35b4-251">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-252">**Varsayılan**: uygulamanın IIS `false` arkasındaki Kestrel, varsayılan olarak olduğu `true`durumlar dışında çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e35b4-253">**Ortam değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e35b4-254">Bu değeri ayarlamak için yapılandırma veya çağırma `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e35b4-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e35b4-255">DetailedErrors</span></span>

<span data-ttu-id="e35b4-256">Etkinleştirildiğinde veya ortam `Development`olduğunda, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e35b4-257">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e35b4-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e35b4-258">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-259">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="e35b4-259">**Default**: `false`</span></span>  
<span data-ttu-id="e35b4-260">**Ortam değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e35b4-261">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e35b4-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e35b4-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="e35b4-263">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="e35b4-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e35b4-264">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e35b4-265">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e35b4-266">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e35b4-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e35b4-267">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-267">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-268">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="e35b4-268">**Default**: Empty string</span></span>  
<span data-ttu-id="e35b4-269">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e35b4-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e35b4-270">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e35b4-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e35b4-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e35b4-272">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="e35b4-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e35b4-273">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e35b4-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e35b4-274">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-274">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-275">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="e35b4-275">**Default**: Empty string</span></span>  
<span data-ttu-id="e35b4-276">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e35b4-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e35b4-277">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e35b4-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e35b4-278">HTTPS_Port</span></span>

<span data-ttu-id="e35b4-279">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="e35b4-279">The HTTPS redirect port.</span></span> <span data-ttu-id="e35b4-280">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e35b4-281">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="e35b4-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="e35b4-282">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-282">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-283">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e35b4-284">**Ortam değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e35b4-285">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e35b4-286">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="e35b4-286">PreferHostingUrls</span></span>

<span data-ttu-id="e35b4-287">Konağın `IWebHostBuilder` `IServer` uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e35b4-288">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e35b4-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e35b4-289">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-290">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="e35b4-290">**Default**: `true`</span></span>  
<span data-ttu-id="e35b4-291">**Ortam değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e35b4-292">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `PreferHostingUrls`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e35b4-293">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="e35b4-293">PreventHostingStartup</span></span>

<span data-ttu-id="e35b4-294">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="e35b4-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e35b4-295">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e35b4-296">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e35b4-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e35b4-297">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-298">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="e35b4-298">**Default**: `false`</span></span>  
<span data-ttu-id="e35b4-299">**Ortam değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e35b4-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e35b4-300">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseSetting` kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e35b4-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e35b4-301">StartupAssembly</span></span>

<span data-ttu-id="e35b4-302">`Startup` Sınıfı aramak için bütünleştirilmiş kod.</span><span class="sxs-lookup"><span data-stu-id="e35b4-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e35b4-303">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e35b4-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e35b4-304">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-304">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-305">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="e35b4-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e35b4-306">**Ortam değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e35b4-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e35b4-307">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseStartup`kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e35b4-308">`UseStartup`bir derleme adı (`string`) veya bir tür (`TStartup`) alabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e35b4-309">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e35b4-310">URL’ler</span><span class="sxs-lookup"><span data-stu-id="e35b4-310">URLs</span></span>

<span data-ttu-id="e35b4-311">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e35b4-312">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e35b4-313">Sunucunun belirtilen\*bağlantı noktasını ve Protokolü (örneğin, `http://*:5000`) kullanarak herhangi bir IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e35b4-314">Protokol (`http://` veya `https://`) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e35b4-315">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="e35b4-316">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="e35b4-316">**Key**: `urls`</span></span>  
<span data-ttu-id="e35b4-317">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-317">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-318">**Varsayılan**: `http://localhost:5000` ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e35b4-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e35b4-319">**Ortam değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e35b4-320">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseUrls`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e35b4-321">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e35b4-322">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e35b4-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-323">WebRoot</span></span>

<span data-ttu-id="e35b4-324">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="e35b4-325">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="e35b4-326">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="e35b4-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="e35b4-327">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-327">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-328">**Varsayılan**: varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e35b4-329">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="e35b4-330">**Ortam değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e35b4-331">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseWebRoot` `IWebHostBuilder`arama yapın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e35b4-332">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-332">For more information, see:</span></span>

* [<span data-ttu-id="e35b4-333">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="e35b4-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e35b4-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e35b4-335">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="e35b4-335">Manage the host lifetime</span></span>

<span data-ttu-id="e35b4-336">Uygulamayı başlatmak ve durdurmak için <xref:Microsoft.Extensions.Hosting.IHost> oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e35b4-337">Bu yöntemler, hizmet <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e35b4-338">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="e35b4-338">Run</span></span>

<span data-ttu-id="e35b4-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="e35b4-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e35b4-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-340">RunAsync</span></span>

<span data-ttu-id="e35b4-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve iptal belirteci veya <xref:System.Threading.Tasks.Task> kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e35b4-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-342">RunConsoleAsync</span></span>

<span data-ttu-id="e35b4-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e35b4-344">Başlat</span><span class="sxs-lookup"><span data-stu-id="e35b4-344">Start</span></span>

<span data-ttu-id="e35b4-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e35b4-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-346">StartAsync</span></span>

<span data-ttu-id="e35b4-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>Konağı başlatır ve iptal belirteci ya <xref:System.Threading.Tasks.Task> da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e35b4-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin `StartAsync`başlangıcında çağrılır ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e35b4-349">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e35b4-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-350">StopAsync</span></span>

<span data-ttu-id="e35b4-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e35b4-352">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="e35b4-352">WaitForShutdown</span></span>

<span data-ttu-id="e35b4-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="e35b4-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e35b4-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="e35b4-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen belirteç <xref:System.Threading.Tasks.Task> ve çağrılar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e35b4-356">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="e35b4-356">External control</span></span>

<span data-ttu-id="e35b4-357">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e35b4-358">ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="e35b4-359">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="e35b4-360">Bu makalede, HTTP isteklerini işlemeyecek uygulamalar<xref:Microsoft.Extensions.Hosting.HostBuilder>Için kullanılan genel ana bilgisayar () ASP.NET Core ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="e35b4-361">Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="e35b4-362">Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="e35b4-363">Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="e35b4-364">Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e35b4-365">Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.</span><span class="sxs-lookup"><span data-stu-id="e35b4-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="e35b4-366">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e35b4-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e35b4-367">Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal*kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="e35b4-368">Örneği bir `internalConsole`içinde çalıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="e35b4-369">Konsolu Visual Studio Code ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="e35b4-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="e35b4-370">*. Vscode/Launch. JSON* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="e35b4-371">**.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="e35b4-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="e35b4-372">Değeri ya da `externalTerminal` olarak ayarlayın `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="e35b4-373">Giriş</span><span class="sxs-lookup"><span data-stu-id="e35b4-373">Introduction</span></span>

<span data-ttu-id="e35b4-374">Genel konak Kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="e35b4-375">`Microsoft.Extensions.Hosting` Paket, [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="e35b4-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="e35b4-376"><xref:Microsoft.Extensions.Hosting.IHostedService>, kod yürütmeye yönelik giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="e35b4-377">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices içinde hizmet kaydı](#configureservices)sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="e35b4-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>, konak başlatıldığında çağrılır <xref:Microsoft.Extensions.Hosting.IHostedService> ve <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ana bilgisayar düzgün bir şekilde kapandığında ters kayıt sırasında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e35b4-379">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="e35b4-379">Set up a host</span></span>

<span data-ttu-id="e35b4-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>, kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="e35b4-381">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e35b4-381">Options</span></span>

<span data-ttu-id="e35b4-382"><xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırın <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="e35b4-383">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="e35b4-383">Shutdown timeout</span></span>

<span data-ttu-id="e35b4-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e35b4-385">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-385">The default value is five seconds.</span></span>

<span data-ttu-id="e35b4-386">İçindeki `Program.Main` aşağıdaki seçenek yapılandırması, varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="e35b4-387">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="e35b4-387">Default services</span></span>

<span data-ttu-id="e35b4-388">Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="e35b4-389">[Ortam](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="e35b4-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="e35b4-390">[Yapılandırma](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="e35b4-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="e35b4-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="e35b4-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="e35b4-393">[Seçenekler](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="e35b4-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="e35b4-394">[Günlüğe kaydetme](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="e35b4-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e35b4-395">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e35b4-395">Host configuration</span></span>

<span data-ttu-id="e35b4-396">Ana bilgisayar yapılandırması şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="e35b4-396">Host configuration is created by:</span></span>

* <span data-ttu-id="e35b4-397"><xref:Microsoft.Extensions.Hosting.IHostBuilder> [İçerik kökünü](#content-root) ve [ortamı](#environment)ayarlamak için uzantı yöntemleri çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="e35b4-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="e35b4-398">İçindeki <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>yapılandırma sağlayıcılarından yapılandırma okunuyor.</span><span class="sxs-lookup"><span data-stu-id="e35b4-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="e35b4-399">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="e35b4-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="e35b4-400">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="e35b4-400">Application key (name)</span></span>

<span data-ttu-id="e35b4-401">[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="e35b4-402">Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="e35b4-403">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="e35b4-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e35b4-404">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-404">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-405">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="e35b4-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="e35b4-406">Şunu **kullanarak ayarla**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="e35b4-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="e35b4-407">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e35b4-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="e35b4-408">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="e35b4-408">Content root</span></span>

<span data-ttu-id="e35b4-409">Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="e35b4-410">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e35b4-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e35b4-411">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-411">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-412">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="e35b4-413">Şunu **kullanarak ayarla**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="e35b4-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="e35b4-414">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e35b4-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e35b4-415">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="e35b4-416">Daha fazla bilgi için bkz. [temel bilgiler: içerik kökü](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="e35b4-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="e35b4-417">Ortam</span><span class="sxs-lookup"><span data-stu-id="e35b4-417">Environment</span></span>

<span data-ttu-id="e35b4-418">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e35b4-419">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="e35b4-419">**Key**: `environment`</span></span>  
<span data-ttu-id="e35b4-420">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-420">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-421">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="e35b4-421">**Default**: `Production`</span></span>  
<span data-ttu-id="e35b4-422">Şunu **kullanarak ayarla**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="e35b4-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="e35b4-423">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e35b4-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e35b4-424">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-424">The environment can be set to any value.</span></span> <span data-ttu-id="e35b4-425">Çerçeve tanımlı değerler, `Development` `Staging`, ve `Production`içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e35b4-426">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="e35b4-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="e35b4-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="e35b4-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>konak <xref:Microsoft.Extensions.Configuration.IConfiguration> için <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> oluşturmak üzere bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="e35b4-429">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın derleme sürecinde kullanılmak üzere başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="e35b4-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e35b4-431">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e35b4-432">Varsayılan olarak hiçbir sağlayıcı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-432">No providers are included by default.</span></span> <span data-ttu-id="e35b4-433">Uygulamanın gerektirdiği her türlü yapılandırma sağlayıcısını açık bir şekilde belirtmeniz gerekir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e35b4-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="e35b4-434">Dosya yapılandırması (örneğin, *HostSettings. JSON* dosyasından).</span><span class="sxs-lookup"><span data-stu-id="e35b4-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="e35b4-435">Ortam değişkeni yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="e35b4-435">Environment variable configuration.</span></span>
* <span data-ttu-id="e35b4-436">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="e35b4-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="e35b4-437">Diğer tüm gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="e35b4-437">Any other required configuration providers.</span></span>

<span data-ttu-id="e35b4-438">Konağın dosya yapılandırması, uygulamanın temel yolu ve `SetBasePath` ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="e35b4-439">Örnek uygulama, bir JSON dosyası, *HostSettings. JSON*ve dosyanın ana bilgisayar <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> yapılandırma ayarlarını kullanmak için çağrılar kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="e35b4-440">Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak Oluşturucu ' ya <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="e35b4-441">`AddEnvironmentVariables`Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="e35b4-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="e35b4-442">Örnek uygulama, öneki kullanır `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="e35b4-443">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e35b4-444">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, için `PREFIX_ENVIRONMENT` ortam değişkeni değeri `environment` anahtar için ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e35b4-445">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya ile `dotnet run`bir uygulama çalıştırırken geliştirme sırasında, *Özellikler/launchsettings. JSON* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="e35b4-446">[Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında *. vscode/Launch. JSON* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="e35b4-447">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e35b4-448">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağırarak <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="e35b4-449">Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="e35b4-450">*HostSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e35b4-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="e35b4-451">Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="e35b4-452">Kullanarak `HostBuilder` <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>örnek yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="e35b4-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="e35b4-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e35b4-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e35b4-454">Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> uygulama çağrısı yaparak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="e35b4-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>uygulama <xref:Microsoft.Extensions.Configuration.IConfiguration> için <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> oluşturmak üzere bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="e35b4-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e35b4-457">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="e35b4-458">Tarafından <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> oluşturulan yapılandırma, sonraki işlemler ve Içinde <xref:Microsoft.Extensions.Hosting.IHost.Services*>, [hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) konumunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="e35b4-459">Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="e35b4-460">Kullanarak <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>örnek uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="e35b4-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="e35b4-461">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e35b4-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="e35b4-462">*appSettings. Development. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e35b4-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="e35b4-463">*appSettings. Production. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e35b4-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="e35b4-464">Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="e35b4-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="e35b4-465">Örnek uygulama, JSON uygulama ayarları dosyalarını ve *HostSettings. JSON* dosyasını şu `<Content>` öğe ile taşıdıkça:</span><span class="sxs-lookup"><span data-stu-id="e35b4-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="e35b4-466">Ve gibi yapılandırma uzantısı yöntemleri, örneğin [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gerektirir. <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*></span><span class="sxs-lookup"><span data-stu-id="e35b4-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="e35b4-467">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadığı takdirde, bu paketlerin temel [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="e35b4-468">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="e35b4-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="e35b4-469">ConfigureServices</span></span>

<span data-ttu-id="e35b4-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e35b4-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e35b4-472">Barındırılan hizmet, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e35b4-473">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="e35b4-474">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , yaşam süresi `AddHostedService` olayları, `LifetimeEventsHostedService`ve zamanlanan bir arka plan görevi `TimedHostedService`için uygulamaya bir hizmet eklemek için genişletme yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="e35b4-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="e35b4-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="e35b4-475">ConfigureLogging</span></span>

<span data-ttu-id="e35b4-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>Belirtilen <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırmayı yapılandırmak için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="e35b4-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="e35b4-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-478">UseConsoleLifetime</span></span>

<span data-ttu-id="e35b4-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm 'i dinler ve kapalı <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="e35b4-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="e35b4-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`Varsayılan yaşam süresi uygulamasına önceden kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="e35b4-482">Kaydedilen son yaşam süresi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="e35b4-483">Kapsayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e35b4-483">Container configuration</span></span>

<span data-ttu-id="e35b4-484">Diğer kapsayıcıları takmayı desteklemek için ana bilgisayar bir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="e35b4-485">Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="e35b4-486">[Useserviceproviderfactory (ıvıceproviderfactory&lt;tcontainerbuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="e35b4-487">Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemiyle yönetilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="e35b4-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="e35b4-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e35b4-490">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e35b4-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="e35b4-491">Hizmet kapsayıcısı fabrikası sağlama:</span><span class="sxs-lookup"><span data-stu-id="e35b4-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="e35b4-492">Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="e35b4-493">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="e35b4-493">Extensibility</span></span>

<span data-ttu-id="e35b4-494">Konak genişletilebilirliği, üzerindeki <xref:Microsoft.Extensions.Hosting.IHostBuilder>genişletme yöntemleriyle gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="e35b4-495">Aşağıdaki örnek, bir genişletme yönteminin ' de <xref:Microsoft.Extensions.Hosting.IHostBuilder> <xref:fundamentals/host/hosted-services>gösterilen [timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir uygulamayı nasıl genişlettiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="e35b4-496">Uygulama, geçirilen barındırılan `UseHostedService` hizmeti kaydetmek için uzantı yöntemi oluşturur `T`:</span><span class="sxs-lookup"><span data-stu-id="e35b4-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="e35b4-497">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="e35b4-497">Manage the host</span></span>

<span data-ttu-id="e35b4-498"><xref:Microsoft.Extensions.Hosting.IHost> Uygulama, hizmet kapsayıcısında kayıtlı olan <xref:Microsoft.Extensions.Hosting.IHostedService> uygulamaları başlatıp durdurmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e35b4-499">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="e35b4-499">Run</span></span>

<span data-ttu-id="e35b4-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="e35b4-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="e35b4-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-501">RunAsync</span></span>

<span data-ttu-id="e35b4-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve iptal belirteci veya <xref:System.Threading.Tasks.Task> kapanışı tetiklendiğinde tamamlanmış bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="e35b4-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="e35b4-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-503">RunConsoleAsync</span></span>

<span data-ttu-id="e35b4-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="e35b4-505">Başlangıç ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-505">Start and StopAsync</span></span>

<span data-ttu-id="e35b4-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="e35b4-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="e35b4-508">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="e35b4-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="e35b4-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="e35b4-511">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="e35b4-511">WaitForShutdown</span></span>

<span data-ttu-id="e35b4-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , (örneğin, <xref:Microsoft.Extensions.Hosting.IHostLifetime> <kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterim dinler) ile tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="e35b4-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="e35b4-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="e35b4-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen belirteç <xref:System.Threading.Tasks.Task> ve çağrılar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="e35b4-516">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="e35b4-516">External control</span></span>

<span data-ttu-id="e35b4-517">Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:</span><span class="sxs-lookup"><span data-stu-id="e35b4-517">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="e35b4-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>başlangıcında çağrılır ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e35b4-519">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="e35b4-520">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="e35b4-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="e35b4-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="e35b4-522">Özelliklerini ve uzantı yöntemlerini kullanmak üzere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="e35b4-523">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="e35b4-524">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="e35b4-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="e35b4-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>düzgün kapanma istekleri de dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="e35b4-526">Arabirimdeki üç özellik, başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek <xref:System.Action> için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="e35b4-527">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="e35b4-527">Cancellation Token</span></span> | <span data-ttu-id="e35b4-528">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="e35b4-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="e35b4-529">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="e35b4-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="e35b4-530">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="e35b4-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="e35b4-531">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-531">All requests should be processed.</span></span> <span data-ttu-id="e35b4-532">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="e35b4-533">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="e35b4-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="e35b4-534">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-534">Requests may still be processing.</span></span> <span data-ttu-id="e35b4-535">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="e35b4-536">Constructor- <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmeti herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e35b4-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="e35b4-537">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , olayları kaydetmek için bir `LifetimeEventsHostedService` sınıfa (bir <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama) ekleme oluşturucusunu kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="e35b4-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="e35b4-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="e35b4-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="e35b4-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="e35b4-540">Aşağıdaki sınıf, sınıfın <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> `Shutdown` yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="e35b4-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e35b4-541">ASP.NET Core şablonları bir .NET Core genel Konağı (<xref:Microsoft.Extensions.Hosting.HostBuilder>) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="e35b4-542">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="e35b4-542">Host definition</span></span>

<span data-ttu-id="e35b4-543">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e35b4-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e35b4-544">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="e35b4-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="e35b4-545">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="e35b4-545">Logging</span></span>
* <span data-ttu-id="e35b4-546">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e35b4-546">Configuration</span></span>
* <span data-ttu-id="e35b4-547">`IHostedService`uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="e35b4-547">`IHostedService` implementations</span></span>

<span data-ttu-id="e35b4-548">Bir konak başlatıldığında, bu uygulamanın, `IHostedService.StartAsync` dı kapsayıcısında bulduğu her <xref:Microsoft.Extensions.Hosting.IHostedService> bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="e35b4-549">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e35b4-550">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="e35b4-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e35b4-551">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="e35b4-551">Set up a host</span></span>

<span data-ttu-id="e35b4-552">Ana bilgisayar genellikle `Program` sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e35b4-553">`Main` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e35b4-553">The `Main` method:</span></span>

* <span data-ttu-id="e35b4-554">Bir Oluşturucu `CreateHostBuilder` nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e35b4-555">Oluşturucu `Build` nesnesindeki `Run` çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e35b4-556">ASP.NET Core Web şablonları, bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e35b4-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="e35b4-557">Aşağıdaki kod, dı kapsayıcısına eklenen bir `IHostedService` uygulamayla http olmayan bir iş yükü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="e35b4-558">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak `CreateHostBuilder` çağrılır: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="e35b4-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e35b4-559">Uygulama Entity Framework Core kullanıyorsa, `CreateHostBuilder` yöntemin adını veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="e35b4-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e35b4-560">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , uygulamayı çalıştırmadan Konağı yapılandıran `CreateHostBuilder` bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e35b4-561">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="e35b4-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e35b4-562">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="e35b4-562">Default builder settings</span></span>

<span data-ttu-id="e35b4-563"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e35b4-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="e35b4-564">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından <xref:System.IO.Directory.GetCurrentDirectory*>döndürülen yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="e35b4-565">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="e35b4-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="e35b4-566">Ön eki olan `DOTNET_`ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e35b4-567">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-567">Command-line arguments.</span></span>
* <span data-ttu-id="e35b4-568">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="e35b4-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="e35b4-569">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e35b4-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="e35b4-570">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e35b4-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e35b4-571">[Secret Manager](xref:security/app-secrets) Uygulama `Development` ortamda çalıştığında gizli dizi Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="e35b4-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e35b4-572">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-572">Environment variables.</span></span>
  * <span data-ttu-id="e35b4-573">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-573">Command-line arguments.</span></span>
* <span data-ttu-id="e35b4-574">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="e35b4-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e35b4-575">Konsol</span><span class="sxs-lookup"><span data-stu-id="e35b4-575">Console</span></span>
  * <span data-ttu-id="e35b4-576">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="e35b4-576">Debug</span></span>
  * <span data-ttu-id="e35b4-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="e35b4-577">EventSource</span></span>
  * <span data-ttu-id="e35b4-578">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="e35b4-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e35b4-579">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e35b4-580">`ConfigureWebHostDefaults` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e35b4-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="e35b4-581">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e35b4-582">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e35b4-583">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options>..</span><span class="sxs-lookup"><span data-stu-id="e35b4-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="e35b4-584">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="e35b4-585">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` Eşitse `true`, [iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e35b4-586">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-586">Enables IIS integration.</span></span> <span data-ttu-id="e35b4-587">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options>..</span><span class="sxs-lookup"><span data-stu-id="e35b4-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e35b4-588">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e35b4-589">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="e35b4-589">Framework-provided services</span></span>

<span data-ttu-id="e35b4-590">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e35b4-591">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e35b4-592">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e35b4-593">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="e35b4-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e35b4-594">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services>..</span><span class="sxs-lookup"><span data-stu-id="e35b4-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e35b4-595">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="e35b4-596">Başlatma sonrası <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> ve düzgün `IApplicationLifetime`kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e35b4-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e35b4-597">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e35b4-598">Arabirim Ayrıca bir `StopApplication` yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e35b4-599">Aşağıdaki örnek, olayları kaydeden `IHostedService` `IHostApplicationLifetime` bir uygulamasıdır:</span><span class="sxs-lookup"><span data-stu-id="e35b4-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e35b4-600">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="e35b4-600">IHostLifetime</span></span>

<span data-ttu-id="e35b4-601"><xref:Microsoft.Extensions.Hosting.IHostLifetime> Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e35b4-602">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-602">The last implementation registered is used.</span></span>

<span data-ttu-id="e35b4-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="e35b4-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e35b4-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e35b4-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e35b4-605"><kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm 'i dinler ve kapalı <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="e35b4-606">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e35b4-607">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="e35b4-607">IHostEnvironment</span></span>

<span data-ttu-id="e35b4-608">Aşağıdaki ayarlarla <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e35b4-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e35b4-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e35b4-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e35b4-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e35b4-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e35b4-611">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="e35b4-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="e35b4-612">Web Apps, `IWebHostEnvironment` [WebRootPath](#webroot)öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="e35b4-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e35b4-613">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e35b4-613">Host configuration</span></span>

<span data-ttu-id="e35b4-614">Ana bilgisayar yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e35b4-615">Konak yapılandırması, içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [Hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="e35b4-616">Sonra `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e35b4-617">Konak yapılandırması eklemek için üzerinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e35b4-618">`ConfigureHostConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e35b4-619">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e35b4-620">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından `CreateDefaultBuilder`dahildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e35b4-621">Web Apps için, ön ekine `ASPNETCORE_` sahip ortam değişkeni sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e35b4-622">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e35b4-623">Örneğin, için `ASPNETCORE_ENVIRONMENT` ortam değişkeni değeri, `environment` anahtar için ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e35b4-624">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e35b4-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e35b4-625">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e35b4-625">App configuration</span></span>

<span data-ttu-id="e35b4-626">Uygulama yapılandırması, üzerinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`çağırarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e35b4-627">`ConfigureAppConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e35b4-628">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e35b4-629">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma, sonraki Işlemler ve dı hizmeti olarak, [hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) konumunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e35b4-630">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e35b4-631">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e35b4-632">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="e35b4-632">Settings for all app types</span></span>

<span data-ttu-id="e35b4-633">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e35b4-634">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e35b4-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e35b4-635">ApplicationName</span></span>

<span data-ttu-id="e35b4-636">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e35b4-637">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="e35b4-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e35b4-638">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-638">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-639">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="e35b4-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e35b4-640">**Ortam değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e35b4-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e35b4-641">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="e35b4-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-642">ContentRoot</span></span>

<span data-ttu-id="e35b4-643">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e35b4-644">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e35b4-645">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e35b4-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e35b4-646">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-646">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-647">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="e35b4-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e35b4-648">**Ortam değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e35b4-649">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseContentRoot` `IHostBuilder`arama yapın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e35b4-650">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-650">For more information, see:</span></span>

* [<span data-ttu-id="e35b4-651">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="e35b4-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e35b4-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e35b4-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e35b4-653">EnvironmentName</span></span>

<span data-ttu-id="e35b4-654">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e35b4-655">Çerçeve tanımlı değerler, `Development` `Staging`, ve `Production`içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e35b4-656">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e35b4-657">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="e35b4-657">**Key**: `environment`</span></span>  
<span data-ttu-id="e35b4-658">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-658">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-659">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="e35b4-659">**Default**: `Production`</span></span>  
<span data-ttu-id="e35b4-660">**Ortam değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e35b4-661">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseEnvironment` `IHostBuilder`arama yapın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e35b4-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e35b4-662">ShutdownTimeout</span></span>

<span data-ttu-id="e35b4-663">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman aşımını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e35b4-664">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-664">The default value is five seconds.</span></span>  <span data-ttu-id="e35b4-665">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="e35b4-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="e35b4-666">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e35b4-667">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e35b4-668">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e35b4-669">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="e35b4-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e35b4-670">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e35b4-671">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e35b4-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e35b4-672">**Şunu yazın**:`int`</span><span class="sxs-lookup"><span data-stu-id="e35b4-672">**Type**: `int`</span></span>  
<span data-ttu-id="e35b4-673">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="e35b4-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e35b4-674">**Ortam değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e35b4-675">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e35b4-676">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="e35b4-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="e35b4-677">Değişiklik sırasında uygulama yapılandırması yeniden yüklemeyi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="e35b4-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="e35b4-678">[Varsayılan](xref:fundamentals/configuration/index#default)olarak, *appSettings. JSON* ve *appSettings. { Ortam}. JSON* , dosya değiştiğinde yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="e35b4-679">ASP.NET Core 5,0 Preview 3 veya sonraki bir sürümde bu yeniden yükleme davranışını devre dışı bırakmak `hostBuilder:reloadConfigOnChange` için anahtarı `false`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="e35b4-680">**Anahtar**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e35b4-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e35b4-681">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-682">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="e35b4-682">**Default**: `true`</span></span>  
<span data-ttu-id="e35b4-683">**Komut satırı bağımsız değişkeni**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e35b4-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e35b4-684">**Ortam değişkeni**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e35b4-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="e35b4-685">İki nokta (`:`) ayırıcısı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="e35b4-686">Daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e35b4-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="e35b4-687">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="e35b4-687">Settings for web apps</span></span>

<span data-ttu-id="e35b4-688">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e35b4-689">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e35b4-690">Üzerinde `IWebHostBuilder` uzantı yöntemleri bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e35b4-691">Aşağıdaki örnekte olduğu gibi, ' nin `webBuilder` `IWebHostBuilder`bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="e35b4-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e35b4-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e35b4-692">CaptureStartupErrors</span></span>

<span data-ttu-id="e35b4-693">Ne `false`zaman, başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="e35b4-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e35b4-694">Ne `true`zaman, ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e35b4-695">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e35b4-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e35b4-696">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-697">**Varsayılan**: uygulamanın IIS `false` arkasındaki Kestrel, varsayılan olarak olduğu `true`durumlar dışında çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e35b4-698">**Ortam değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e35b4-699">Bu değeri ayarlamak için yapılandırma veya çağırma `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e35b4-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e35b4-700">DetailedErrors</span></span>

<span data-ttu-id="e35b4-701">Etkinleştirildiğinde veya ortam `Development`olduğunda, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="e35b4-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e35b4-702">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e35b4-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e35b4-703">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-704">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="e35b4-704">**Default**: `false`</span></span>  
<span data-ttu-id="e35b4-705">**Ortam değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e35b4-706">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e35b4-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e35b4-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="e35b4-708">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="e35b4-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e35b4-709">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e35b4-710">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e35b4-711">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e35b4-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e35b4-712">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-712">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-713">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="e35b4-713">**Default**: Empty string</span></span>  
<span data-ttu-id="e35b4-714">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e35b4-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e35b4-715">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e35b4-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e35b4-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e35b4-717">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="e35b4-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e35b4-718">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e35b4-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e35b4-719">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-719">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-720">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="e35b4-720">**Default**: Empty string</span></span>  
<span data-ttu-id="e35b4-721">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e35b4-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e35b4-722">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e35b4-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e35b4-723">HTTPS_Port</span></span>

<span data-ttu-id="e35b4-724">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="e35b4-724">The HTTPS redirect port.</span></span> <span data-ttu-id="e35b4-725">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e35b4-726">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="e35b4-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="e35b4-727">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-727">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-728">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e35b4-729">**Ortam değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e35b4-730">Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e35b4-731">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="e35b4-731">PreferHostingUrls</span></span>

<span data-ttu-id="e35b4-732">Konağın `IWebHostBuilder` `IServer` uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e35b4-733">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e35b4-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e35b4-734">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-735">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="e35b4-735">**Default**: `true`</span></span>  
<span data-ttu-id="e35b4-736">**Ortam değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e35b4-737">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `PreferHostingUrls`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e35b4-738">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="e35b4-738">PreventHostingStartup</span></span>

<span data-ttu-id="e35b4-739">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="e35b4-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e35b4-740">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e35b4-741">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e35b4-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e35b4-742">**Tür**: `bool` (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="e35b4-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e35b4-743">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="e35b4-743">**Default**: `false`</span></span>  
<span data-ttu-id="e35b4-744">**Ortam değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e35b4-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e35b4-745">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseSetting` kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e35b4-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e35b4-746">StartupAssembly</span></span>

<span data-ttu-id="e35b4-747">`Startup` Sınıfı aramak için bütünleştirilmiş kod.</span><span class="sxs-lookup"><span data-stu-id="e35b4-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e35b4-748">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e35b4-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e35b4-749">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-749">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-750">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="e35b4-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e35b4-751">**Ortam değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e35b4-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e35b4-752">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseStartup`kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e35b4-753">`UseStartup`bir derleme adı (`string`) veya bir tür (`TStartup`) alabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e35b4-754">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e35b4-755">URL’ler</span><span class="sxs-lookup"><span data-stu-id="e35b4-755">URLs</span></span>

<span data-ttu-id="e35b4-756">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="e35b4-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e35b4-757">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e35b4-758">Sunucunun belirtilen\*bağlantı noktasını ve Protokolü (örneğin, `http://*:5000`) kullanarak herhangi bir IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e35b4-759">Protokol (`http://` veya `https://`) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e35b4-760">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="e35b4-761">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="e35b4-761">**Key**: `urls`</span></span>  
<span data-ttu-id="e35b4-762">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-762">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-763">**Varsayılan**: `http://localhost:5000` ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e35b4-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e35b4-764">**Ortam değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e35b4-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e35b4-765">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseUrls`kullanın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e35b4-766">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e35b4-767">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e35b4-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e35b4-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-768">WebRoot</span></span>

<span data-ttu-id="e35b4-769">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="e35b4-770">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="e35b4-771">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="e35b4-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="e35b4-772">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="e35b4-772">**Type**: `string`</span></span>  
<span data-ttu-id="e35b4-773">**Varsayılan**: varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="e35b4-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e35b4-774">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="e35b4-775">**Ortam değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e35b4-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e35b4-776">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseWebRoot` `IWebHostBuilder`arama yapın:</span><span class="sxs-lookup"><span data-stu-id="e35b4-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e35b4-777">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e35b4-777">For more information, see:</span></span>

* [<span data-ttu-id="e35b4-778">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="e35b4-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e35b4-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e35b4-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e35b4-780">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="e35b4-780">Manage the host lifetime</span></span>

<span data-ttu-id="e35b4-781">Uygulamayı başlatmak ve durdurmak için <xref:Microsoft.Extensions.Hosting.IHost> oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="e35b4-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e35b4-782">Bu yöntemler, hizmet <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e35b4-783">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="e35b4-783">Run</span></span>

<span data-ttu-id="e35b4-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="e35b4-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e35b4-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-785">RunAsync</span></span>

<span data-ttu-id="e35b4-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve iptal belirteci veya <xref:System.Threading.Tasks.Task> kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e35b4-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-787">RunConsoleAsync</span></span>

<span data-ttu-id="e35b4-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e35b4-789">Başlat</span><span class="sxs-lookup"><span data-stu-id="e35b4-789">Start</span></span>

<span data-ttu-id="e35b4-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e35b4-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-791">StartAsync</span></span>

<span data-ttu-id="e35b4-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>Konağı başlatır ve iptal belirteci ya <xref:System.Threading.Tasks.Task> da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e35b4-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin `StartAsync`başlangıcında çağrılır ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="e35b4-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e35b4-794">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e35b4-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e35b4-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-795">StopAsync</span></span>

<span data-ttu-id="e35b4-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e35b4-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e35b4-797">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="e35b4-797">WaitForShutdown</span></span>

<span data-ttu-id="e35b4-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd>+<kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="e35b4-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e35b4-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e35b4-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="e35b4-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen belirteç <xref:System.Threading.Tasks.Task> ve çağrılar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="e35b4-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e35b4-801">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="e35b4-801">External control</span></span>

<span data-ttu-id="e35b4-802">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="e35b4-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e35b4-803">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e35b4-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
