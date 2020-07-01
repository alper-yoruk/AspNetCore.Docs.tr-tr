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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 5a2d39af6c921323ae9113fd4aca27dcdedd44a5
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793468"
---
# <a name="net-generic-host"></a><span data-ttu-id="0ff05-103">.NET genel ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="0ff05-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="0ff05-104">ASP.NET Core şablonları bir .NET Core genel ana bilgisayarı oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="0ff05-105">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="0ff05-105">Host definition</span></span>

<span data-ttu-id="0ff05-106">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0ff05-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0ff05-107">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="0ff05-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="0ff05-108">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="0ff05-108">Logging</span></span>
* <span data-ttu-id="0ff05-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0ff05-109">Configuration</span></span>
* <span data-ttu-id="0ff05-110">`IHostedService`uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="0ff05-110">`IHostedService` implementations</span></span>

<span data-ttu-id="0ff05-111">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="0ff05-112">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0ff05-113">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="0ff05-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0ff05-114">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ff05-114">Set up a host</span></span>

<span data-ttu-id="0ff05-115">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0ff05-116">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0ff05-116">The `Main` method:</span></span>

* <span data-ttu-id="0ff05-117">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0ff05-118">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0ff05-119">ASP.NET Core Web şablonları, genel bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0ff05-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="0ff05-120">Aşağıdaki kod, HTTP olmayan iş yükünü kullanan genel bir konak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="0ff05-121">`IHostedService`Uygulama, dı kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="0ff05-122">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0ff05-123">Yukarıdaki kod ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="0ff05-124">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0ff05-125">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0ff05-126">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0ff05-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0ff05-127">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="0ff05-127">Default builder settings</span></span>

<span data-ttu-id="0ff05-128"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0ff05-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="0ff05-129">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="0ff05-130">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0ff05-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="0ff05-131">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0ff05-132">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-132">Command-line arguments.</span></span>
* <span data-ttu-id="0ff05-133">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0ff05-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="0ff05-134">*Üzerindeappsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="0ff05-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="0ff05-135">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0ff05-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0ff05-136">Uygulama ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) `Development` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0ff05-137">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-137">Environment variables.</span></span>
  * <span data-ttu-id="0ff05-138">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-138">Command-line arguments.</span></span>
* <span data-ttu-id="0ff05-139">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="0ff05-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0ff05-140">Konsol</span><span class="sxs-lookup"><span data-stu-id="0ff05-140">Console</span></span>
  * <span data-ttu-id="0ff05-141">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="0ff05-141">Debug</span></span>
  * <span data-ttu-id="0ff05-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="0ff05-142">EventSource</span></span>
  * <span data-ttu-id="0ff05-143">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="0ff05-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0ff05-144">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0ff05-145">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0ff05-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0ff05-146">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0ff05-147">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0ff05-148">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="0ff05-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0ff05-149">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0ff05-150">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0ff05-151">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-151">Enables IIS integration.</span></span> <span data-ttu-id="0ff05-152">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="0ff05-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0ff05-153">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0ff05-154">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0ff05-154">Framework-provided services</span></span>

<span data-ttu-id="0ff05-155">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0ff05-156">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0ff05-157">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0ff05-158">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="0ff05-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0ff05-159">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="0ff05-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0ff05-160">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="0ff05-161"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ff05-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0ff05-162">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0ff05-163">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0ff05-164">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0ff05-165">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-165">IHostLifetime</span></span>

<span data-ttu-id="0ff05-166"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0ff05-167">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-167">The last implementation registered is used.</span></span>

<span data-ttu-id="0ff05-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="0ff05-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0ff05-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0ff05-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0ff05-170"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="0ff05-171">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0ff05-172">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="0ff05-172">IHostEnvironment</span></span>

<span data-ttu-id="0ff05-173"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ff05-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0ff05-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0ff05-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0ff05-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0ff05-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0ff05-176">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="0ff05-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0ff05-177">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [WebRootPath](#webroot)</span><span class="sxs-lookup"><span data-stu-id="0ff05-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0ff05-178">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0ff05-178">Host configuration</span></span>

<span data-ttu-id="0ff05-179">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0ff05-180">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="0ff05-181">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0ff05-182">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0ff05-183">`ConfigureHostConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0ff05-184">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0ff05-185">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0ff05-186">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0ff05-187">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0ff05-188">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0ff05-189">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0ff05-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0ff05-190">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0ff05-190">App configuration</span></span>

<span data-ttu-id="0ff05-191">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0ff05-192">`ConfigureAppConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0ff05-193">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0ff05-194">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0ff05-195">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0ff05-196">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0ff05-197">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="0ff05-197">Settings for all app types</span></span>

<span data-ttu-id="0ff05-198">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0ff05-199">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0ff05-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0ff05-200">ApplicationName</span></span>

<span data-ttu-id="0ff05-201">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0ff05-202">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="0ff05-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0ff05-203">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-203">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-204">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="0ff05-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0ff05-205">**Ortam değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0ff05-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0ff05-206">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0ff05-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-207">ContentRoot</span></span>

<span data-ttu-id="0ff05-208">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0ff05-209">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0ff05-210">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0ff05-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0ff05-211">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-211">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-212">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="0ff05-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0ff05-213">**Ortam değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0ff05-214">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0ff05-215">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-215">For more information, see:</span></span>

* [<span data-ttu-id="0ff05-216">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="0ff05-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0ff05-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0ff05-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0ff05-218">EnvironmentName</span></span>

<span data-ttu-id="0ff05-219">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0ff05-220">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0ff05-221">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0ff05-222">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="0ff05-222">**Key**: `environment`</span></span>  
<span data-ttu-id="0ff05-223">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-223">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-224">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="0ff05-224">**Default**: `Production`</span></span>  
<span data-ttu-id="0ff05-225">**Ortam değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0ff05-226">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0ff05-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0ff05-227">ShutdownTimeout</span></span>

<span data-ttu-id="0ff05-228">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0ff05-229">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-229">The default value is five seconds.</span></span>  <span data-ttu-id="0ff05-230">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="0ff05-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="0ff05-231">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0ff05-232">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0ff05-233">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0ff05-234">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0ff05-235">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0ff05-236">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0ff05-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0ff05-237">**Şunu yazın**:`int`</span><span class="sxs-lookup"><span data-stu-id="0ff05-237">**Type**: `int`</span></span>  
<span data-ttu-id="0ff05-238">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="0ff05-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0ff05-239">**Ortam değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0ff05-240">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0ff05-241">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0ff05-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="0ff05-242">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="0ff05-242">Settings for web apps</span></span>

<span data-ttu-id="0ff05-243">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0ff05-244">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0ff05-245">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0ff05-246">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="0ff05-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0ff05-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0ff05-247">CaptureStartupErrors</span></span>

<span data-ttu-id="0ff05-248">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="0ff05-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0ff05-249">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0ff05-250">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0ff05-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0ff05-251">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-252">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0ff05-253">**Ortam değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0ff05-254">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0ff05-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0ff05-255">DetailedErrors</span></span>

<span data-ttu-id="0ff05-256">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0ff05-257">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0ff05-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0ff05-258">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-259">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="0ff05-259">**Default**: `false`</span></span>  
<span data-ttu-id="0ff05-260">**Ortam değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0ff05-261">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0ff05-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0ff05-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="0ff05-263">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0ff05-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0ff05-264">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0ff05-265">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0ff05-266">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0ff05-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0ff05-267">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-267">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-268">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0ff05-268">**Default**: Empty string</span></span>  
<span data-ttu-id="0ff05-269">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0ff05-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0ff05-270">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0ff05-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0ff05-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0ff05-272">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0ff05-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0ff05-273">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0ff05-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0ff05-274">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-274">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-275">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0ff05-275">**Default**: Empty string</span></span>  
<span data-ttu-id="0ff05-276">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0ff05-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0ff05-277">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0ff05-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0ff05-278">HTTPS_Port</span></span>

<span data-ttu-id="0ff05-279">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="0ff05-279">The HTTPS redirect port.</span></span> <span data-ttu-id="0ff05-280">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0ff05-281">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="0ff05-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="0ff05-282">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-282">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-283">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0ff05-284">**Ortam değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0ff05-285">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0ff05-286">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="0ff05-286">PreferHostingUrls</span></span>

<span data-ttu-id="0ff05-287">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0ff05-288">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0ff05-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0ff05-289">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-290">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="0ff05-290">**Default**: `true`</span></span>  
<span data-ttu-id="0ff05-291">**Ortam değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0ff05-292">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0ff05-293">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="0ff05-293">PreventHostingStartup</span></span>

<span data-ttu-id="0ff05-294">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="0ff05-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0ff05-295">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0ff05-296">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0ff05-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0ff05-297">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-298">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="0ff05-298">**Default**: `false`</span></span>  
<span data-ttu-id="0ff05-299">**Ortam değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0ff05-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0ff05-300">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0ff05-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0ff05-301">StartupAssembly</span></span>

<span data-ttu-id="0ff05-302">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0ff05-303">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0ff05-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0ff05-304">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-304">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-305">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="0ff05-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0ff05-306">**Ortam değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0ff05-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0ff05-307">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0ff05-308">`UseStartup`bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0ff05-309">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0ff05-310">URL’ler</span><span class="sxs-lookup"><span data-stu-id="0ff05-310">URLs</span></span>

<span data-ttu-id="0ff05-311">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0ff05-312">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0ff05-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0ff05-313">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0ff05-314">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0ff05-315">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="0ff05-316">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="0ff05-316">**Key**: `urls`</span></span>  
<span data-ttu-id="0ff05-317">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-317">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-318">**Varsayılan**: `http://localhost:5000` ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0ff05-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0ff05-319">**Ortam değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0ff05-320">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0ff05-321">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0ff05-322">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0ff05-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-323">WebRoot</span></span>

<span data-ttu-id="0ff05-324">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0ff05-325">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0ff05-326">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="0ff05-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="0ff05-327">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-327">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-328">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0ff05-329">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0ff05-330">**Ortam değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0ff05-331">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0ff05-332">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-332">For more information, see:</span></span>

* [<span data-ttu-id="0ff05-333">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="0ff05-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0ff05-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0ff05-335">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="0ff05-335">Manage the host lifetime</span></span>

<span data-ttu-id="0ff05-336"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0ff05-337">Bu yöntemler <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0ff05-338">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="0ff05-338">Run</span></span>

<span data-ttu-id="0ff05-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0ff05-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0ff05-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-340">RunAsync</span></span>

<span data-ttu-id="0ff05-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0ff05-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0ff05-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-342">RunConsoleAsync</span></span>

<span data-ttu-id="0ff05-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0ff05-344">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="0ff05-344">Start</span></span>

<span data-ttu-id="0ff05-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0ff05-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-346">StartAsync</span></span>

<span data-ttu-id="0ff05-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0ff05-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0ff05-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0ff05-349">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0ff05-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-350">StopAsync</span></span>

<span data-ttu-id="0ff05-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0ff05-352">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="0ff05-352">WaitForShutdown</span></span>

<span data-ttu-id="0ff05-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0ff05-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0ff05-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="0ff05-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0ff05-356">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="0ff05-356">External control</span></span>

<span data-ttu-id="0ff05-357">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0ff05-358">ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="0ff05-359">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="0ff05-360">Bu makalede <xref:Microsoft.Extensions.Hosting.HostBuilder> , http isteklerini işlemeyecek uygulamalar için kullanılan genel ana bilgisayar () ASP.NET Core ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="0ff05-361">Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="0ff05-362">Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="0ff05-363">Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="0ff05-364">Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="0ff05-365">Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.</span><span class="sxs-lookup"><span data-stu-id="0ff05-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="0ff05-366">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ff05-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0ff05-367">Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal*kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="0ff05-368">Örneği bir içinde çalıştırmayın `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="0ff05-369">Konsolu Visual Studio Code ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="0ff05-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="0ff05-370">Dosyasında *. vscode/launch.js* açın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="0ff05-371">**.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="0ff05-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="0ff05-372">Değeri ya da olarak ayarlayın `externalTerminal` `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="0ff05-373">Giriş</span><span class="sxs-lookup"><span data-stu-id="0ff05-373">Introduction</span></span>

<span data-ttu-id="0ff05-374">Genel konak kitaplığı ad alanında kullanılabilir <xref:Microsoft.Extensions.Hosting> ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="0ff05-375">`Microsoft.Extensions.Hosting`Paket, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="0ff05-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="0ff05-376"><xref:Microsoft.Extensions.Hosting.IHostedService>, kod yürütmeye yönelik giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="0ff05-377">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices içinde hizmet kaydı](#configureservices)sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="0ff05-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="0ff05-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>, <xref:Microsoft.Extensions.Hosting.IHostedService> konak başlatıldığında çağrılır ve <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ana bilgisayar düzgün bir şekilde kapandığında ters kayıt sırasında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0ff05-379">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ff05-379">Set up a host</span></span>

<span data-ttu-id="0ff05-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>, kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="0ff05-381">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="0ff05-381">Options</span></span>

<span data-ttu-id="0ff05-382"><xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırın <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="0ff05-383">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="0ff05-383">Shutdown timeout</span></span>

<span data-ttu-id="0ff05-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0ff05-385">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-385">The default value is five seconds.</span></span>

<span data-ttu-id="0ff05-386">İçindeki aşağıdaki seçenek yapılandırması, `Program.Main` varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="0ff05-387">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0ff05-387">Default services</span></span>

<span data-ttu-id="0ff05-388">Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="0ff05-389">[Ortam](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="0ff05-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="0ff05-390">[Yapılandırma](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="0ff05-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="0ff05-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0ff05-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="0ff05-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0ff05-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="0ff05-393">[Seçenekler](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="0ff05-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="0ff05-394">[Günlüğe kaydetme](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="0ff05-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0ff05-395">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0ff05-395">Host configuration</span></span>

<span data-ttu-id="0ff05-396">Ana bilgisayar yapılandırması şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0ff05-396">Host configuration is created by:</span></span>

* <span data-ttu-id="0ff05-397"><xref:Microsoft.Extensions.Hosting.IHostBuilder> [İçerik kökünü](#content-root) ve [ortamı](#environment)ayarlamak için uzantı yöntemleri çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="0ff05-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="0ff05-398">İçindeki yapılandırma sağlayıcılarından yapılandırma okunuyor <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="0ff05-399">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0ff05-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="0ff05-400">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="0ff05-400">Application key (name)</span></span>

<span data-ttu-id="0ff05-401">[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="0ff05-402">Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ff05-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="0ff05-403">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="0ff05-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0ff05-404">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-404">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-405">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="0ff05-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="0ff05-406">Şunu **kullanarak ayarla**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="0ff05-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="0ff05-407">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0ff05-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="0ff05-408">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="0ff05-408">Content root</span></span>

<span data-ttu-id="0ff05-409">Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="0ff05-410">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0ff05-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0ff05-411">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-411">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-412">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="0ff05-413">Şunu **kullanarak ayarla**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="0ff05-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="0ff05-414">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0ff05-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0ff05-415">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="0ff05-416">Daha fazla bilgi için bkz. [temel bilgiler: içerik kökü](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="0ff05-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="0ff05-417">Ortam</span><span class="sxs-lookup"><span data-stu-id="0ff05-417">Environment</span></span>

<span data-ttu-id="0ff05-418">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0ff05-419">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="0ff05-419">**Key**: `environment`</span></span>  
<span data-ttu-id="0ff05-420">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-420">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-421">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="0ff05-421">**Default**: `Production`</span></span>  
<span data-ttu-id="0ff05-422">Şunu **kullanarak ayarla**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="0ff05-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="0ff05-423">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0ff05-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0ff05-424">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-424">The environment can be set to any value.</span></span> <span data-ttu-id="0ff05-425">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0ff05-426">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="0ff05-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="0ff05-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="0ff05-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>konak için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="0ff05-429">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın derleme sürecinde kullanılmak üzere başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="0ff05-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0ff05-431">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0ff05-432">Varsayılan olarak hiçbir sağlayıcı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-432">No providers are included by default.</span></span> <span data-ttu-id="0ff05-433">Uygulamanın gerektirdiği her türlü yapılandırma sağlayıcısını açık bir şekilde belirtmeniz gerekir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , örneğin:</span><span class="sxs-lookup"><span data-stu-id="0ff05-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="0ff05-434">Dosya yapılandırması (örneğin, dosyadaki bir *hostsettings.js* ).</span><span class="sxs-lookup"><span data-stu-id="0ff05-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="0ff05-435">Ortam değişkeni yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="0ff05-435">Environment variable configuration.</span></span>
* <span data-ttu-id="0ff05-436">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="0ff05-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="0ff05-437">Diğer tüm gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="0ff05-437">Any other required configuration providers.</span></span>

<span data-ttu-id="0ff05-438">Konağın dosya yapılandırması, uygulamanın temel yolu ve `SetBasePath` ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="0ff05-439">Örnek uygulama, *üzerindehostsettings.js*bir JSON dosyası ve <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> dosyanın ana bilgisayar yapılandırma ayarlarını kullanmak için çağrılar kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="0ff05-440">Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak Oluşturucu ' ya çağrı yapın <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="0ff05-441">`AddEnvironmentVariables`Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="0ff05-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="0ff05-442">Örnek uygulama, öneki kullanır `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="0ff05-443">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0ff05-444">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, için ortam değişkeni değeri `PREFIX_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0ff05-445">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya ile bir uygulama çalıştırırken geliştirme sırasında `dotnet run` , ortam değişkenleri dosyadaki *Özellikler/launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="0ff05-446">[Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında dosya *üzerinde. vscode/launch.js* içinde ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="0ff05-447">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0ff05-448">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağırarak eklenir <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="0ff05-449">Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="0ff05-450">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="0ff05-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="0ff05-451">Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="0ff05-452">`HostBuilder`Kullanarak örnek yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="0ff05-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="0ff05-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="0ff05-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="0ff05-454">Uygulama yapılandırması, uygulama çağrısı yaparak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="0ff05-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>uygulama için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="0ff05-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0ff05-457">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="0ff05-458">Tarafından oluşturulan yapılandırma, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sonraki işlemler ve içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bölümünde mevcuttur <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="0ff05-459">Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="0ff05-460">Kullanarak örnek uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="0ff05-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="0ff05-461">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="0ff05-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="0ff05-462">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="0ff05-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="0ff05-463">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="0ff05-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="0ff05-464">Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="0ff05-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="0ff05-465">Örnek uygulama, JSON uygulama ayarları dosyalarını ve *hostsettings.js* aşağıdaki öğeyle taşıdıkça `<Content>` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="0ff05-466">Ve gibi yapılandırma uzantısı yöntemleri, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.Js](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) veMicrosoft.Extensions.Configuration gibi ek NuGet paketleri gerektirir [. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="0ff05-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="0ff05-467">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadıkça, bu paketlerin çekirdek [Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="0ff05-468">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="0ff05-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="0ff05-469">ConfigureServices</span></span>

<span data-ttu-id="0ff05-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0ff05-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0ff05-472">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0ff05-473">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="0ff05-474">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `AddHostedService` yaşam süresi olayları, `LifetimeEventsHostedService` ve zamanlanan bir arka plan görevi için uygulamaya bir hizmet eklemek için genişletme yöntemini kullanır `TimedHostedService` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="0ff05-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="0ff05-475">ConfigureLogging</span></span>

<span data-ttu-id="0ff05-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>belirtilen yapılandırmayı yapılandırmak için bir temsilci ekler <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="0ff05-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="0ff05-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-478">UseConsoleLifetime</span></span>

<span data-ttu-id="0ff05-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="0ff05-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="0ff05-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`Varsayılan yaşam süresi uygulamasına önceden kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="0ff05-482">Kaydedilen son yaşam süresi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="0ff05-483">Kapsayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0ff05-483">Container configuration</span></span>

<span data-ttu-id="0ff05-484">Diğer kapsayıcıları takmayı desteklemek için ana bilgisayar bir kabul edebilir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="0ff05-485">Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="0ff05-486">[Useserviceproviderfactory (ıvıceproviderfactory &lt; tcontainerbuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="0ff05-487">Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemiyle yönetilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="0ff05-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="0ff05-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0ff05-490">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ff05-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="0ff05-491">Hizmet kapsayıcısı fabrikası sağlama:</span><span class="sxs-lookup"><span data-stu-id="0ff05-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="0ff05-492">Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="0ff05-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="0ff05-493">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="0ff05-493">Extensibility</span></span>

<span data-ttu-id="0ff05-494">Konak genişletilebilirliği, üzerindeki genişletme yöntemleriyle gerçekleştirilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="0ff05-495">Aşağıdaki örnek, bir genişletme yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> ' de gösterilen [Timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir uygulamayı nasıl genişlettiğini gösterir <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="0ff05-496">Uygulama, `UseHostedService` geçirilen barındırılan hizmeti kaydetmek için uzantı yöntemi oluşturur `T` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="0ff05-497">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="0ff05-497">Manage the host</span></span>

<span data-ttu-id="0ff05-498"><xref:Microsoft.Extensions.Hosting.IHost>Uygulama, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı olan uygulamaları başlatıp durdurmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0ff05-499">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="0ff05-499">Run</span></span>

<span data-ttu-id="0ff05-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="0ff05-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="0ff05-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-501">RunAsync</span></span>

<span data-ttu-id="0ff05-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="0ff05-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="0ff05-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-503">RunConsoleAsync</span></span>

<span data-ttu-id="0ff05-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="0ff05-505">Başlangıç ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-505">Start and StopAsync</span></span>

<span data-ttu-id="0ff05-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="0ff05-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="0ff05-508">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="0ff05-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="0ff05-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="0ff05-511">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="0ff05-511">WaitForShutdown</span></span>

<span data-ttu-id="0ff05-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, (örneğin, <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterim dinler) ile tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="0ff05-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="0ff05-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="0ff05-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="0ff05-516">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="0ff05-516">External control</span></span>

<span data-ttu-id="0ff05-517">Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:</span><span class="sxs-lookup"><span data-stu-id="0ff05-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0ff05-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin başlangıcında çağrılır <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0ff05-519">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="0ff05-520">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="0ff05-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="0ff05-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="0ff05-522">Özelliklerini ve uzantı yöntemlerini kullanmak üzere sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> :</span><span class="sxs-lookup"><span data-stu-id="0ff05-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="0ff05-523">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="0ff05-524">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="0ff05-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="0ff05-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>düzgün kapanma istekleri de dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="0ff05-526">Arabirimdeki üç özellik, <xref:System.Action> Başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="0ff05-527">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="0ff05-527">Cancellation Token</span></span> | <span data-ttu-id="0ff05-528">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="0ff05-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="0ff05-529">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="0ff05-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="0ff05-530">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="0ff05-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="0ff05-531">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-531">All requests should be processed.</span></span> <span data-ttu-id="0ff05-532">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="0ff05-533">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="0ff05-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="0ff05-534">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-534">Requests may still be processing.</span></span> <span data-ttu-id="0ff05-535">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="0ff05-536">Constructor- <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmeti herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ff05-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="0ff05-537">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `LifetimeEventsHostedService` olayları kaydetmek için bir sınıfa (bir uygulama) ekleme oluşturucusunu kullanır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="0ff05-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ff05-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="0ff05-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="0ff05-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="0ff05-540">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır `Shutdown` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="0ff05-541">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0ff05-542">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="0ff05-542">Host definition</span></span>

<span data-ttu-id="0ff05-543">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0ff05-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0ff05-544">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="0ff05-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="0ff05-545">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="0ff05-545">Logging</span></span>
* <span data-ttu-id="0ff05-546">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0ff05-546">Configuration</span></span>
* <span data-ttu-id="0ff05-547">`IHostedService`uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="0ff05-547">`IHostedService` implementations</span></span>

<span data-ttu-id="0ff05-548">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="0ff05-549">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0ff05-550">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="0ff05-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0ff05-551">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ff05-551">Set up a host</span></span>

<span data-ttu-id="0ff05-552">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0ff05-553">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0ff05-553">The `Main` method:</span></span>

* <span data-ttu-id="0ff05-554">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0ff05-555">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0ff05-556">ASP.NET Core Web şablonları, bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0ff05-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="0ff05-557">Aşağıdaki kod, dı kapsayıcısına eklenen bir uygulamayla HTTP olmayan bir iş yükü oluşturur `IHostedService` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="0ff05-558">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0ff05-559">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0ff05-560">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0ff05-561">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0ff05-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0ff05-562">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="0ff05-562">Default builder settings</span></span>

<span data-ttu-id="0ff05-563"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0ff05-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="0ff05-564">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="0ff05-565">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0ff05-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="0ff05-566">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0ff05-567">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-567">Command-line arguments.</span></span>
* <span data-ttu-id="0ff05-568">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0ff05-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="0ff05-569">*Üzerindeappsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="0ff05-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="0ff05-570">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0ff05-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0ff05-571">Uygulama ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) `Development` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0ff05-572">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-572">Environment variables.</span></span>
  * <span data-ttu-id="0ff05-573">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-573">Command-line arguments.</span></span>
* <span data-ttu-id="0ff05-574">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="0ff05-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0ff05-575">Konsol</span><span class="sxs-lookup"><span data-stu-id="0ff05-575">Console</span></span>
  * <span data-ttu-id="0ff05-576">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="0ff05-576">Debug</span></span>
  * <span data-ttu-id="0ff05-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="0ff05-577">EventSource</span></span>
  * <span data-ttu-id="0ff05-578">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="0ff05-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0ff05-579">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0ff05-580">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0ff05-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0ff05-581">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0ff05-582">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0ff05-583">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="0ff05-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0ff05-584">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0ff05-585">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0ff05-586">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-586">Enables IIS integration.</span></span> <span data-ttu-id="0ff05-587">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="0ff05-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0ff05-588">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0ff05-589">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0ff05-589">Framework-provided services</span></span>

<span data-ttu-id="0ff05-590">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0ff05-591">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0ff05-592">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0ff05-593">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="0ff05-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0ff05-594">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="0ff05-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0ff05-595">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="0ff05-596"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ff05-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0ff05-597">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0ff05-598">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0ff05-599">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0ff05-600">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0ff05-600">IHostLifetime</span></span>

<span data-ttu-id="0ff05-601"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0ff05-602">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-602">The last implementation registered is used.</span></span>

<span data-ttu-id="0ff05-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="0ff05-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0ff05-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0ff05-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0ff05-605"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="0ff05-606">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0ff05-607">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="0ff05-607">IHostEnvironment</span></span>

<span data-ttu-id="0ff05-608"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ff05-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0ff05-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0ff05-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0ff05-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0ff05-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0ff05-611">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="0ff05-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0ff05-612">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [WebRootPath](#webroot)</span><span class="sxs-lookup"><span data-stu-id="0ff05-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0ff05-613">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0ff05-613">Host configuration</span></span>

<span data-ttu-id="0ff05-614">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0ff05-615">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="0ff05-616">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0ff05-617">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0ff05-618">`ConfigureHostConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0ff05-619">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0ff05-620">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0ff05-621">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0ff05-622">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0ff05-623">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0ff05-624">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0ff05-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0ff05-625">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0ff05-625">App configuration</span></span>

<span data-ttu-id="0ff05-626">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0ff05-627">`ConfigureAppConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0ff05-628">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0ff05-629">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0ff05-630">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0ff05-631">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0ff05-632">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="0ff05-632">Settings for all app types</span></span>

<span data-ttu-id="0ff05-633">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0ff05-634">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0ff05-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0ff05-635">ApplicationName</span></span>

<span data-ttu-id="0ff05-636">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0ff05-637">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="0ff05-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0ff05-638">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-638">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-639">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="0ff05-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0ff05-640">**Ortam değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0ff05-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0ff05-641">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0ff05-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-642">ContentRoot</span></span>

<span data-ttu-id="0ff05-643">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0ff05-644">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0ff05-645">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0ff05-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0ff05-646">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-646">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-647">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="0ff05-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0ff05-648">**Ortam değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0ff05-649">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0ff05-650">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-650">For more information, see:</span></span>

* [<span data-ttu-id="0ff05-651">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="0ff05-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0ff05-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0ff05-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0ff05-653">EnvironmentName</span></span>

<span data-ttu-id="0ff05-654">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0ff05-655">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0ff05-656">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0ff05-657">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="0ff05-657">**Key**: `environment`</span></span>  
<span data-ttu-id="0ff05-658">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-658">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-659">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="0ff05-659">**Default**: `Production`</span></span>  
<span data-ttu-id="0ff05-660">**Ortam değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0ff05-661">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0ff05-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0ff05-662">ShutdownTimeout</span></span>

<span data-ttu-id="0ff05-663">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0ff05-664">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-664">The default value is five seconds.</span></span>  <span data-ttu-id="0ff05-665">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="0ff05-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="0ff05-666">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0ff05-667">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0ff05-668">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0ff05-669">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="0ff05-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0ff05-670">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0ff05-671">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0ff05-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0ff05-672">**Şunu yazın**:`int`</span><span class="sxs-lookup"><span data-stu-id="0ff05-672">**Type**: `int`</span></span>  
<span data-ttu-id="0ff05-673">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="0ff05-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0ff05-674">**Ortam değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0ff05-675">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0ff05-676">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0ff05-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="0ff05-677">Değişiklik sırasında uygulama yapılandırması yeniden yüklemeyi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0ff05-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="0ff05-678">[Varsayılan](xref:fundamentals/configuration/index#default)olarak, *appsettings.json* ve appSettingsappsettings.js *. { Ortam}. JSON* , dosya değiştiğinde yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="0ff05-679">ASP.NET Core 5,0 Preview 3 veya sonraki bir sürümde bu yeniden yükleme davranışını devre dışı bırakmak için `hostBuilder:reloadConfigOnChange` anahtarı olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="0ff05-680">**Anahtar**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0ff05-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0ff05-681">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-682">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="0ff05-682">**Default**: `true`</span></span>  
<span data-ttu-id="0ff05-683">**Komut satırı bağımsız değişkeni**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0ff05-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0ff05-684">**Ortam değişkeni**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0ff05-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="0ff05-685">İki nokta ( `:` ) ayırıcısı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="0ff05-686">Daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="0ff05-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="0ff05-687">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="0ff05-687">Settings for web apps</span></span>

<span data-ttu-id="0ff05-688">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0ff05-689">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0ff05-690">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0ff05-691">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="0ff05-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0ff05-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0ff05-692">CaptureStartupErrors</span></span>

<span data-ttu-id="0ff05-693">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="0ff05-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0ff05-694">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0ff05-695">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0ff05-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0ff05-696">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-697">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0ff05-698">**Ortam değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0ff05-699">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0ff05-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0ff05-700">DetailedErrors</span></span>

<span data-ttu-id="0ff05-701">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="0ff05-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0ff05-702">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0ff05-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0ff05-703">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-704">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="0ff05-704">**Default**: `false`</span></span>  
<span data-ttu-id="0ff05-705">**Ortam değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0ff05-706">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0ff05-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0ff05-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="0ff05-708">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0ff05-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0ff05-709">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0ff05-710">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0ff05-711">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0ff05-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0ff05-712">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-712">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-713">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0ff05-713">**Default**: Empty string</span></span>  
<span data-ttu-id="0ff05-714">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0ff05-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0ff05-715">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0ff05-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0ff05-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0ff05-717">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0ff05-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0ff05-718">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0ff05-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0ff05-719">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-719">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-720">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0ff05-720">**Default**: Empty string</span></span>  
<span data-ttu-id="0ff05-721">**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0ff05-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0ff05-722">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0ff05-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0ff05-723">HTTPS_Port</span></span>

<span data-ttu-id="0ff05-724">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="0ff05-724">The HTTPS redirect port.</span></span> <span data-ttu-id="0ff05-725">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0ff05-726">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="0ff05-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="0ff05-727">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-727">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-728">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0ff05-729">**Ortam değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0ff05-730">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0ff05-731">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="0ff05-731">PreferHostingUrls</span></span>

<span data-ttu-id="0ff05-732">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0ff05-733">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0ff05-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0ff05-734">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-735">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="0ff05-735">**Default**: `true`</span></span>  
<span data-ttu-id="0ff05-736">**Ortam değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0ff05-737">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0ff05-738">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="0ff05-738">PreventHostingStartup</span></span>

<span data-ttu-id="0ff05-739">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="0ff05-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0ff05-740">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0ff05-741">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0ff05-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0ff05-742">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0ff05-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0ff05-743">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="0ff05-743">**Default**: `false`</span></span>  
<span data-ttu-id="0ff05-744">**Ortam değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0ff05-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0ff05-745">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0ff05-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0ff05-746">StartupAssembly</span></span>

<span data-ttu-id="0ff05-747">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0ff05-748">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0ff05-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0ff05-749">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-749">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-750">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="0ff05-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0ff05-751">**Ortam değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0ff05-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0ff05-752">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0ff05-753">`UseStartup`bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0ff05-754">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0ff05-755">URL’ler</span><span class="sxs-lookup"><span data-stu-id="0ff05-755">URLs</span></span>

<span data-ttu-id="0ff05-756">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="0ff05-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0ff05-757">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0ff05-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0ff05-758">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0ff05-759">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0ff05-760">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="0ff05-761">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="0ff05-761">**Key**: `urls`</span></span>  
<span data-ttu-id="0ff05-762">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-762">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-763">**Varsayılan**: `http://localhost:5000` ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0ff05-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0ff05-764">**Ortam değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0ff05-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0ff05-765">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0ff05-766">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0ff05-767">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0ff05-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0ff05-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-768">WebRoot</span></span>

<span data-ttu-id="0ff05-769">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0ff05-770">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0ff05-771">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="0ff05-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="0ff05-772">**Şunu yazın**:`string`</span><span class="sxs-lookup"><span data-stu-id="0ff05-772">**Type**: `string`</span></span>  
<span data-ttu-id="0ff05-773">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0ff05-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0ff05-774">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0ff05-775">**Ortam değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0ff05-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0ff05-776">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ff05-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0ff05-777">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0ff05-777">For more information, see:</span></span>

* [<span data-ttu-id="0ff05-778">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="0ff05-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0ff05-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0ff05-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0ff05-780">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="0ff05-780">Manage the host lifetime</span></span>

<span data-ttu-id="0ff05-781"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="0ff05-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0ff05-782">Bu yöntemler <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0ff05-783">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="0ff05-783">Run</span></span>

<span data-ttu-id="0ff05-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0ff05-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0ff05-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-785">RunAsync</span></span>

<span data-ttu-id="0ff05-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0ff05-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0ff05-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-787">RunConsoleAsync</span></span>

<span data-ttu-id="0ff05-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0ff05-789">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="0ff05-789">Start</span></span>

<span data-ttu-id="0ff05-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0ff05-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-791">StartAsync</span></span>

<span data-ttu-id="0ff05-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0ff05-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0ff05-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="0ff05-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0ff05-794">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ff05-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0ff05-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-795">StopAsync</span></span>

<span data-ttu-id="0ff05-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ff05-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0ff05-797">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="0ff05-797">WaitForShutdown</span></span>

<span data-ttu-id="0ff05-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0ff05-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0ff05-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0ff05-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="0ff05-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0ff05-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0ff05-801">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="0ff05-801">External control</span></span>

<span data-ttu-id="0ff05-802">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="0ff05-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0ff05-803">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0ff05-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
