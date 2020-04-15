---
title: .NET Genel Ana Bilgisayar
author: rick-anderson
description: Uygulama başlatma ve yaşam boyu yönetimden sorumlu .NET Core Generic Host hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384031"
---
# <a name="net-generic-host"></a><span data-ttu-id="5e029-103">.NET Genel Ana Bilgisayar</span><span class="sxs-lookup"><span data-stu-id="5e029-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="5e029-104">Core şablonlarının ASP.NET .NET Core Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5e029-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="5e029-105">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="5e029-105">Host definition</span></span>

<span data-ttu-id="5e029-106">*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:</span><span class="sxs-lookup"><span data-stu-id="5e029-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5e029-107">Bağımlılık enjeksiyonu (DI)</span><span class="sxs-lookup"><span data-stu-id="5e029-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="5e029-108">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="5e029-108">Logging</span></span>
* <span data-ttu-id="5e029-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5e029-109">Configuration</span></span>
* <span data-ttu-id="5e029-110">`IHostedService`Uygulama</span><span class="sxs-lookup"><span data-stu-id="5e029-110">`IHostedService` implementations</span></span>

<span data-ttu-id="5e029-111">Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="5e029-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="5e029-112">Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5e029-113">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="5e029-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5e029-114">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="5e029-114">Set up a host</span></span>

<span data-ttu-id="5e029-115">Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5e029-116">Yöntem: `Main`</span><span class="sxs-lookup"><span data-stu-id="5e029-116">The `Main` method:</span></span>

* <span data-ttu-id="5e029-117">Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="5e029-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5e029-118">Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.</span><span class="sxs-lookup"><span data-stu-id="5e029-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5e029-119">ASP.NET Core web şablonları bir ana bilgisayar oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5e029-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="5e029-120">Aşağıdaki kod, DI kapsayıcısına eklenen `IHostedService` bir uygulamayla HTTP dışı bir iş yükü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5e029-120">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="5e029-121">Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:</span><span class="sxs-lookup"><span data-stu-id="5e029-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5e029-122">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="5e029-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5e029-123">[Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5e029-124">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="5e029-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5e029-125">Varsayılan oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="5e029-125">Default builder settings</span></span>

<span data-ttu-id="5e029-126">Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="5e029-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="5e029-127">İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="5e029-128">Yükler ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5e029-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="5e029-129">Çevre değişkenleri . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="5e029-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5e029-130">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-130">Command-line arguments.</span></span>
* <span data-ttu-id="5e029-131">Uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5e029-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="5e029-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5e029-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="5e029-133">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="5e029-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5e029-134">[Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="5e029-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5e029-135">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-135">Environment variables.</span></span>
  * <span data-ttu-id="5e029-136">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-136">Command-line arguments.</span></span>
* <span data-ttu-id="5e029-137">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="5e029-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5e029-138">Konsol</span><span class="sxs-lookup"><span data-stu-id="5e029-138">Console</span></span>
  * <span data-ttu-id="5e029-139">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="5e029-139">Debug</span></span>
  * <span data-ttu-id="5e029-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="5e029-140">EventSource</span></span>
  * <span data-ttu-id="5e029-141">EventLog (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="5e029-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5e029-142">Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)</span><span class="sxs-lookup"><span data-stu-id="5e029-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5e029-143">Yöntem: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="5e029-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5e029-144">`ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.</span><span class="sxs-lookup"><span data-stu-id="5e029-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5e029-145">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5e029-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5e029-146">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5e029-147">[Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5e029-148">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5e029-149">IIS tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-149">Enables IIS integration.</span></span> <span data-ttu-id="5e029-150">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5e029-151">Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5e029-152">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="5e029-152">Framework-provided services</span></span>

<span data-ttu-id="5e029-153">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5e029-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5e029-154">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5e029-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5e029-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5e029-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5e029-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5e029-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5e029-157">Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5e029-158">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5e029-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="5e029-159">Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="5e029-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5e029-160">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="5e029-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5e029-161">Arabirim de `StopApplication` bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="5e029-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5e029-162">Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="5e029-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5e029-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5e029-163">IHostLifetime</span></span>

<span data-ttu-id="5e029-164">Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler.</span><span class="sxs-lookup"><span data-stu-id="5e029-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5e029-165">Son kayıtlı uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-165">The last implementation registered is used.</span></span>

<span data-ttu-id="5e029-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="5e029-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5e029-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5e029-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5e029-168"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="5e029-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="5e029-169">[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="5e029-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5e029-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5e029-170">IHostEnvironment</span></span>

<span data-ttu-id="5e029-171">Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="5e029-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5e029-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5e029-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5e029-173">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5e029-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5e029-174">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5e029-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="5e029-175">Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="5e029-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5e029-176">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5e029-176">Host configuration</span></span>

<span data-ttu-id="5e029-177">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5e029-178">Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="5e029-179">Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5e029-180">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın.</span><span class="sxs-lookup"><span data-stu-id="5e029-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5e029-181">`ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5e029-182">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5e029-183">Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="5e029-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5e029-184">Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5e029-185">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5e029-186">Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="5e029-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5e029-187">Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5e029-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5e029-188">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5e029-188">App configuration</span></span>

<span data-ttu-id="5e029-189">Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5e029-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5e029-190">`ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5e029-191">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5e029-192">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5e029-193">Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5e029-194">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.</span><span class="sxs-lookup"><span data-stu-id="5e029-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5e029-195">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5e029-195">Settings for all app types</span></span>

<span data-ttu-id="5e029-196">Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5e029-197">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5e029-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5e029-198">ApplicationName</span></span>

<span data-ttu-id="5e029-199">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5e029-200">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="5e029-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5e029-201">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-201">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-202">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="5e029-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5e029-203">**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5e029-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5e029-204">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5e029-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="5e029-205">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="5e029-205">ContentRoot</span></span>

<span data-ttu-id="5e029-206">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5e029-207">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="5e029-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5e029-208">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5e029-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5e029-209">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-209">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-210">**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="5e029-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5e029-211">**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5e029-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5e029-212">Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5e029-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5e029-213">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-213">For more information, see:</span></span>

* [<span data-ttu-id="5e029-214">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="5e029-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5e029-215">Webroot</span><span class="sxs-lookup"><span data-stu-id="5e029-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5e029-216">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5e029-216">EnvironmentName</span></span>

<span data-ttu-id="5e029-217">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5e029-218">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="5e029-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5e029-219">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5e029-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5e029-220">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="5e029-220">**Key**: `environment`</span></span>  
<span data-ttu-id="5e029-221">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-221">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-222">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="5e029-222">**Default**: `Production`</span></span>  
<span data-ttu-id="5e029-223">**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5e029-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5e029-224">Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5e029-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5e029-225">KapatmaTimeout</span><span class="sxs-lookup"><span data-stu-id="5e029-225">ShutdownTimeout</span></span>

<span data-ttu-id="5e029-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5e029-227">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5e029-227">The default value is five seconds.</span></span>  <span data-ttu-id="5e029-228">Zaman açılığı dönüşü nde, ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="5e029-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="5e029-229">Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5e029-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5e029-230">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.</span><span class="sxs-lookup"><span data-stu-id="5e029-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5e029-231">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="5e029-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5e029-232">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="5e029-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5e029-233">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="5e029-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5e029-234">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5e029-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5e029-235">**Türü**:`int`</span><span class="sxs-lookup"><span data-stu-id="5e029-235">**Type**: `int`</span></span>  
<span data-ttu-id="5e029-236">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="5e029-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5e029-237">**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5e029-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5e029-238">Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions`</span><span class="sxs-lookup"><span data-stu-id="5e029-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5e029-239">Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5e029-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="5e029-240">Web uygulamaları için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5e029-240">Settings for web apps</span></span>

<span data-ttu-id="5e029-241">Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5e029-242">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5e029-243">Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="5e029-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5e029-244">Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="5e029-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5e029-245">YakalamaBaşlangıç Hataları</span><span class="sxs-lookup"><span data-stu-id="5e029-245">CaptureStartupErrors</span></span>

<span data-ttu-id="5e029-246">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="5e029-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5e029-247">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="5e029-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5e029-248">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5e029-248">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5e029-249">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-249">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-250">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="5e029-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5e029-251">**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5e029-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5e029-252">Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5e029-253">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="5e029-253">DetailedErrors</span></span>

<span data-ttu-id="5e029-254">Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="5e029-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5e029-255">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5e029-255">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5e029-256">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-256">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-257">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5e029-257">**Default**: `false`</span></span>  
<span data-ttu-id="5e029-258">**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5e029-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5e029-259">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5e029-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5e029-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="5e029-261">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5e029-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5e029-262">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="5e029-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5e029-263">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5e029-264">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5e029-264">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5e029-265">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-265">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-266">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5e029-266">**Default**: Empty string</span></span>  
<span data-ttu-id="5e029-267">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5e029-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5e029-268">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5e029-269">HostingStartupExcludeMontajies</span><span class="sxs-lookup"><span data-stu-id="5e029-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5e029-270">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5e029-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5e029-271">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5e029-271">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5e029-272">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-272">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-273">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5e029-273">**Default**: Empty string</span></span>  
<span data-ttu-id="5e029-274">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5e029-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5e029-275">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5e029-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5e029-276">HTTPS_Port</span></span>

<span data-ttu-id="5e029-277">HTTPS yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="5e029-277">The HTTPS redirect port.</span></span> <span data-ttu-id="5e029-278">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5e029-279">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="5e029-279">**Key**: `https_port`</span></span>  
<span data-ttu-id="5e029-280">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-280">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-281">**Varsayılan**: Varsayılan değer ayarlı değil.</span><span class="sxs-lookup"><span data-stu-id="5e029-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5e029-282">**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5e029-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5e029-283">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5e029-284">TercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5e029-284">PreferHostingUrls</span></span>

<span data-ttu-id="5e029-285">Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5e029-286">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5e029-286">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5e029-287">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-287">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-288">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="5e029-288">**Default**: `true`</span></span>  
<span data-ttu-id="5e029-289">**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5e029-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5e029-290">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5e029-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5e029-291">HostingBaşlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="5e029-291">PreventHostingStartup</span></span>

<span data-ttu-id="5e029-292">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5e029-293">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5e029-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5e029-294">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5e029-294">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5e029-295">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-295">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-296">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5e029-296">**Default**: `false`</span></span>  
<span data-ttu-id="5e029-297">**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5e029-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5e029-298">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5e029-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5e029-299">Başlangıç Montaj</span><span class="sxs-lookup"><span data-stu-id="5e029-299">StartupAssembly</span></span>

<span data-ttu-id="5e029-300">`Startup` Sınıfı aramak için derleme.</span><span class="sxs-lookup"><span data-stu-id="5e029-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5e029-301">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5e029-301">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5e029-302">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-302">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-303">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="5e029-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5e029-304">**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5e029-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5e029-305">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="5e029-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5e029-306">`UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir).</span><span class="sxs-lookup"><span data-stu-id="5e029-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5e029-307">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5e029-308">URL’ler</span><span class="sxs-lookup"><span data-stu-id="5e029-308">URLs</span></span>

<span data-ttu-id="5e029-309">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi.</span><span class="sxs-lookup"><span data-stu-id="5e029-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5e029-310">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5e029-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5e029-311">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="5e029-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5e029-312">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5e029-313">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="5e029-314">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="5e029-314">**Key**: `urls`</span></span>  
<span data-ttu-id="5e029-315">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-315">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-316">**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5e029-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5e029-317">**Çevre değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5e029-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5e029-318">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="5e029-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5e029-319">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="5e029-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5e029-320">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5e029-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5e029-321">Webroot</span><span class="sxs-lookup"><span data-stu-id="5e029-321">WebRoot</span></span>

<span data-ttu-id="5e029-322">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği uygulamanın statik varlıklarıiçin göreli yolu belirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-322">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="5e029-323">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-323">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="5e029-324">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="5e029-324">**Key**: `webroot`</span></span>  
<span data-ttu-id="5e029-325">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-325">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-326">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="5e029-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5e029-327">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5e029-327">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="5e029-328">**Çevre değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5e029-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5e029-329">Bu değeri ayarlamak için, ortam `UseWebRoot` değişkenini kullanın veya çağırın: `IWebHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5e029-329">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5e029-330">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-330">For more information, see:</span></span>

* [<span data-ttu-id="5e029-331">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="5e029-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5e029-332">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="5e029-332">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5e029-333">Ev sahibinin ömrünü yönetin</span><span class="sxs-lookup"><span data-stu-id="5e029-333">Manage the host lifetime</span></span>

<span data-ttu-id="5e029-334">Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın.</span><span class="sxs-lookup"><span data-stu-id="5e029-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5e029-335">Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="5e029-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5e029-336">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5e029-336">Run</span></span>

<span data-ttu-id="5e029-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5e029-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5e029-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-338">RunAsync</span></span>

<span data-ttu-id="5e029-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.</span><span class="sxs-lookup"><span data-stu-id="5e029-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5e029-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-340">RunConsoleAsync</span></span>

<span data-ttu-id="5e029-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5e029-342">Başlat</span><span class="sxs-lookup"><span data-stu-id="5e029-342">Start</span></span>

<span data-ttu-id="5e029-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="5e029-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5e029-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-344">StartAsync</span></span>

<span data-ttu-id="5e029-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir.</span><span class="sxs-lookup"><span data-stu-id="5e029-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5e029-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="5e029-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5e029-347">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5e029-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-348">StopAsync</span></span>

<span data-ttu-id="5e029-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5e029-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5e029-350">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="5e029-350">WaitForShutdown</span></span>

<span data-ttu-id="5e029-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.</span><span class="sxs-lookup"><span data-stu-id="5e029-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5e029-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="5e029-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="5e029-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5e029-354">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="5e029-354">External control</span></span>

<span data-ttu-id="5e029-355">Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="5e029-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5e029-356">ASP.NET Core uygulamaları bir ana bilgisayar yapılandırır ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="5e029-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="5e029-357">Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="5e029-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="5e029-358">Bu makale, HTTP isteklerini<xref:Microsoft.Extensions.Hosting.HostBuilder>işlemeyan uygulamalar için kullanılan ASP.NET Core Genel Ana Bilgisayar (),</span><span class="sxs-lookup"><span data-stu-id="5e029-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="5e029-359">Genel Ana Bilgisayar'ın amacı, daha geniş bir ana bilgisayar senaryosu dizisi sağlamak için HTTP ardışık hattını Web Ana Bilgisayar API'sinden ayırmaktır.</span><span class="sxs-lookup"><span data-stu-id="5e029-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="5e029-360">Genel Ana Bilgisayar'a dayalı mesajlaşma, arka plan görevleri ve diğer HTTP dışındaki iş yükleri yapılandırma, bağımlılık enjeksiyonu (DI) ve günlüğe kaydetme gibi çapraz kesme özelliklerinden yararlanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="5e029-361">Genel Ana Bilgisayar, ASP.NET Core 2.1'de yenidir ve web barındırma senaryoları için uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="5e029-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="5e029-362">Web barındırma senaryoları için [Web Barındıran Alanı'nı](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="5e029-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5e029-363">Genel Ana Bilgisayar, gelecekteki bir sürümde Web Host'un yerini alır ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API'si olarak hareket eder.</span><span class="sxs-lookup"><span data-stu-id="5e029-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="5e029-364">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e029-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5e029-365">Örnek uygulamayı Visual [Studio Code'da](https://code.visualstudio.com/)çalıştırırken *harici veya entegre*bir terminal kullanın.</span><span class="sxs-lookup"><span data-stu-id="5e029-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="5e029-366">Örneği bir `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="5e029-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="5e029-367">Konsolu Visual Studio Code'da ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="5e029-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="5e029-368">*.vscode/launch.json* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="5e029-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="5e029-369">**.NET Core Launch (konsol)** yapılandırmasında, **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="5e029-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="5e029-370">Değeri ya da `externalTerminal` `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="5e029-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="5e029-371">Giriş</span><span class="sxs-lookup"><span data-stu-id="5e029-371">Introduction</span></span>

<span data-ttu-id="5e029-372">Genel Ana Bilgisayar kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="5e029-373">Paket `Microsoft.Extensions.Hosting` [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (core 2.1 veya daha sonra ASP.NET) dahildir.</span><span class="sxs-lookup"><span data-stu-id="5e029-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="5e029-374"><xref:Microsoft.Extensions.Hosting.IHostedService>kod yürütmeiçin giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="5e029-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="5e029-375">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama [ConfigureServices hizmet kaydı](#configureservices)sırasına göre yürütülür.</span><span class="sxs-lookup"><span data-stu-id="5e029-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="5e029-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>ana bilgisayar <xref:Microsoft.Extensions.Hosting.IHostedService> başladığında her birine <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> çağrılır ve ana bilgisayar zarif bir şekilde kapandığında ters kayıt sırasına çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5e029-377">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="5e029-377">Set up a host</span></span>

<span data-ttu-id="5e029-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder>kitaplıkların ve uygulamaların ana bilgisayarı başlatma, oluşturmak ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="5e029-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="5e029-379">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="5e029-379">Options</span></span>

<span data-ttu-id="5e029-380"><xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırmak <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="5e029-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="5e029-381">Kapatma zaman</span><span class="sxs-lookup"><span data-stu-id="5e029-381">Shutdown timeout</span></span>

<span data-ttu-id="5e029-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5e029-383">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5e029-383">The default value is five seconds.</span></span>

<span data-ttu-id="5e029-384">Aşağıdaki seçenek yapılandırmasında `Program.Main` varsayılan beş saniyelik kapatma zaman dışarısını 20 saniyeye çıkarır:</span><span class="sxs-lookup"><span data-stu-id="5e029-384">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="5e029-385">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="5e029-385">Default services</span></span>

<span data-ttu-id="5e029-386">Aşağıdaki hizmetler ana bilgisayar başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5e029-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="5e029-387">[Çevre](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="5e029-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="5e029-388">[Yapılandırma](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="5e029-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="5e029-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5e029-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="5e029-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5e029-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="5e029-391">[Seçenekler](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="5e029-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="5e029-392">[Günlük](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="5e029-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5e029-393">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5e029-393">Host configuration</span></span>

<span data-ttu-id="5e029-394">Ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5e029-394">Host configuration is created by:</span></span>

* <span data-ttu-id="5e029-395">İçerik <xref:Microsoft.Extensions.Hosting.IHostBuilder> [kökünü](#content-root) ve [ortamını](#environment)ayarlamak için uzantı yöntemlerini çağırma.</span><span class="sxs-lookup"><span data-stu-id="5e029-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="5e029-396">Yapılandırma sağlayıcılarından yapılandırmayı <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>okuma.</span><span class="sxs-lookup"><span data-stu-id="5e029-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="5e029-397">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="5e029-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="5e029-398">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="5e029-398">Application key (name)</span></span>

<span data-ttu-id="5e029-399">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği, ana bilgisayar inşaatı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="5e029-400">Değeri açıkça ayarlamak için [HostDefaults.ApplicationKey:'i](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="5e029-401">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="5e029-401">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5e029-402">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-402">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-403">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="5e029-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="5e029-404">**Set kullanarak:**`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="5e029-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="5e029-405">**Çevre değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5e029-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="5e029-406">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="5e029-406">Content root</span></span>

<span data-ttu-id="5e029-407">Bu ayar, ana bilgisayarın içerik dosyalarını aramaya nerede başlayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="5e029-408">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5e029-408">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5e029-409">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-409">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-410">**Varsayılan**: Varsayılan olarak uygulama derlemesinin bulunduğu klasöre iner.</span><span class="sxs-lookup"><span data-stu-id="5e029-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="5e029-411">**Set kullanarak:**`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="5e029-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="5e029-412">**Çevre değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5e029-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5e029-413">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="5e029-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="5e029-414">Daha fazla bilgi için [temel bilgiler: İçerik kökü.](xref:fundamentals/index#content-root)</span><span class="sxs-lookup"><span data-stu-id="5e029-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="5e029-415">Ortam</span><span class="sxs-lookup"><span data-stu-id="5e029-415">Environment</span></span>

<span data-ttu-id="5e029-416">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5e029-417">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="5e029-417">**Key**: `environment`</span></span>  
<span data-ttu-id="5e029-418">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-418">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-419">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="5e029-419">**Default**: `Production`</span></span>  
<span data-ttu-id="5e029-420">**Set kullanarak:**`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="5e029-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="5e029-421">**Çevre değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5e029-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5e029-422">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-422">The environment can be set to any value.</span></span> <span data-ttu-id="5e029-423">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="5e029-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5e029-424">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5e029-424">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="5e029-425">YapılandırmaHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="5e029-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="5e029-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ana <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> bilgisayar için <xref:Microsoft.Extensions.Configuration.IConfiguration> bir oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="5e029-427">Ana bilgisayar yapılandırması, uygulamanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> yapı işleminde kullanım için başlatma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="5e029-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5e029-429">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5e029-430">Hiçbir sağlayıcı varsayılan olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="5e029-430">No providers are included by default.</span></span> <span data-ttu-id="5e029-431">Aşağıdakiler de dahil olmak üzere, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>uygulamanın gerektirdiği yapılandırma sağlayıcılarını açıkça belirtmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="5e029-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="5e029-432">Dosya yapılandırması (örneğin, bir *hostsettings.json* dosyasından).</span><span class="sxs-lookup"><span data-stu-id="5e029-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="5e029-433">Çevre değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="5e029-433">Environment variable configuration.</span></span>
* <span data-ttu-id="5e029-434">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="5e029-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="5e029-435">Diğer gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="5e029-435">Any other required configuration providers.</span></span>

<span data-ttu-id="5e029-436">Ana bilgisayar dosya yapılandırması, uygulamanın temel yolunu `SetBasePath` belirterek ve ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrıyla etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="5e029-437">Örnek uygulama bir JSON dosyası, *hostsettings.json*ve dosyanın ana bilgisayar yapılandırma ayarlarını tüketmek için aramalar <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="5e029-438">Ana bilgisayara [ortam değişkenyapılandırması](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için ana bilgisayar oluşturucuyu arayın. <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*></span><span class="sxs-lookup"><span data-stu-id="5e029-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="5e029-439">`AddEnvironmentVariables`isteğe bağlı kullanıcı tanımlı öneki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5e029-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="5e029-440">Örnek uygulama bir önek `PREFIX_`kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="5e029-441">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5e029-442">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, ortam `PREFIX_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="5e029-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5e029-443">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya bir `dotnet run`uygulamayı çalıştırırken geliştirme *sırasında, özellikler/launchSettings.json* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="5e029-444">[Visual Studio Code'da](https://code.visualstudio.com/)ortam değişkenleri geliştirme sırasında *.vscode/launch.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="5e029-445">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5e029-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5e029-446">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağrılar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>eklenmez.</span><span class="sxs-lookup"><span data-stu-id="5e029-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="5e029-447">Komut satırı yapılandırması, komut satırı bağımsız değişkenlerini önceki yapılandırma sağlayıcıları tarafından sağlanan yapılandırmayı geçersiz kılmak için en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="5e029-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5e029-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="5e029-449">Ek yapılandırma [applicationName](#application-key-name) ve [contentRoot](#content-root) tuşları ile sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="5e029-450">Örnek `HostBuilder` yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>kullanarak:</span><span class="sxs-lookup"><span data-stu-id="5e029-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="5e029-451">YapılandırmaAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5e029-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5e029-452">Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostBuilder> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uygulama çağırılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5e029-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="5e029-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>uygulama <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="5e029-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5e029-455">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="5e029-456">Tarafından <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) kullanılabilir ve <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="5e029-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="5e029-457">Uygulama [yapılandırması, ConfigureHostConfiguration](#configurehostconfiguration)tarafından sağlanan ana bilgisayar yapılandırması otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="5e029-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="5e029-458">Örnek uygulama <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>yapılandırması kullanarak:</span><span class="sxs-lookup"><span data-stu-id="5e029-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="5e029-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5e029-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="5e029-460">*ayarları. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="5e029-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="5e029-461">*ayarları. Üretim.json*:</span><span class="sxs-lookup"><span data-stu-id="5e029-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="5e029-462">Ayarlar dosyalarını çıktı dizinine taşımak için, proje dosyasındaki ayarlar dosyalarını [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="5e029-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="5e029-463">Örnek uygulama JSON uygulama ayarları dosyalarını ve *hostsettings.json'u* aşağıdaki `<Content>` öğeyle hareket ettirir:</span><span class="sxs-lookup"><span data-stu-id="5e029-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="5e029-464"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gibi yapılandırma uzantısı yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="5e029-465">Uygulama [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmadığı sürece, bu paketler in core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="5e029-466">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5e029-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="5e029-467">YapılandırmaHizmetleri</span><span class="sxs-lookup"><span data-stu-id="5e029-467">ConfigureServices</span></span>

<span data-ttu-id="5e029-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) konteynerine hizmetler ekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5e029-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5e029-470">Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="5e029-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="5e029-471">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="5e029-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="5e029-472">[Örnek uygulama,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` `LifetimeEventsHostedService`yaşam boyu olaylar için bir hizmet ve zamanlanmış arka `TimedHostedService`plan görevi, uygulamaya eklemek için uzantı yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="5e029-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="5e029-473">YapılandırmaLogging</span><span class="sxs-lookup"><span data-stu-id="5e029-473">ConfigureLogging</span></span>

<span data-ttu-id="5e029-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>sağlanan <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırma için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="5e029-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="5e029-476">UseConsoleÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5e029-476">UseConsoleLifetime</span></span>

<span data-ttu-id="5e029-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'i dinler ve kapatma işlemini başlatmak için çağrıda bulunur. <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*></span><span class="sxs-lookup"><span data-stu-id="5e029-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="5e029-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="5e029-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="5e029-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan ömür boyu uygulama olarak önceden kaydedilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5e029-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="5e029-480">Son ömür boyu kayıtlı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="5e029-481">Konteyner yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5e029-481">Container configuration</span></span>

<span data-ttu-id="5e029-482">Diğer kapsayıcılara takMayı desteklemek için, <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>ana bilgisayar bir .</span><span class="sxs-lookup"><span data-stu-id="5e029-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="5e029-483">Bir fabrika sağlamak DI konteyner kaydının bir parçası değildir, ancak bunun yerine beton DI konteyner oluşturmak için kullanılan bir ana bilgisayardır.</span><span class="sxs-lookup"><span data-stu-id="5e029-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="5e029-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder),&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) uygulamanın servis sağlayıcısını oluşturmak için kullanılan varsayılan fabrikayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="5e029-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="5e029-485">Özel kapsayıcı yapılandırmayöntemi <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> tarafından yönetilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="5e029-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>kapsayıcıyı temel ana bilgisayar API'sinin üzerine yapılandırmak için güçlü bir şekilde yazılmamış bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="5e029-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5e029-488">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="5e029-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="5e029-489">Bir servis konteyner fabrikası sağlayın:</span><span class="sxs-lookup"><span data-stu-id="5e029-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="5e029-490">Fabrikayı kullanın ve uygulama için özel servis konteynerini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="5e029-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="5e029-491">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="5e029-491">Extensibility</span></span>

<span data-ttu-id="5e029-492">Ana bilgisayar genişletilebilirliği uzantısı yöntemleri <xref:Microsoft.Extensions.Hosting.IHostBuilder>ile gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="5e029-493">Aşağıdaki örnek, uzantı yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> [timedHostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneğinde gösterildiği bir <xref:fundamentals/host/hosted-services>uygulamayı nasıl genişletir gösteriş olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="5e029-494">Bir uygulama, `UseHostedService` barındırılan hizmeti kaydetmek için `T`uzantı yöntemini belirler:</span><span class="sxs-lookup"><span data-stu-id="5e029-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="5e029-495">Ev sahibini yönetme</span><span class="sxs-lookup"><span data-stu-id="5e029-495">Manage the host</span></span>

<span data-ttu-id="5e029-496">Uygulama, <xref:Microsoft.Extensions.Hosting.IHost> hizmet konteynerine <xref:Microsoft.Extensions.Hosting.IHostedService> kayıtlı uygulamaların başlatılmasından ve durdurulmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="5e029-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5e029-497">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5e029-497">Run</span></span>

<span data-ttu-id="5e029-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırAn ve ana bilgisayar kapatılana kadar arama iş parçacığı engeller:</span><span class="sxs-lookup"><span data-stu-id="5e029-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="5e029-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-499">RunAsync</span></span>

<span data-ttu-id="5e029-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür:</span><span class="sxs-lookup"><span data-stu-id="5e029-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="5e029-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-501">RunConsoleAsync</span></span>

<span data-ttu-id="5e029-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="5e029-503">Başlat ve DurdurAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-503">Start and StopAsync</span></span>

<span data-ttu-id="5e029-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="5e029-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="5e029-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5e029-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="5e029-506">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="5e029-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="5e029-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="5e029-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="5e029-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="5e029-509">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="5e029-509">WaitForShutdown</span></span>

<span data-ttu-id="5e029-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><xref:Microsoft.Extensions.Hosting.IHostLifetime>(Ctrl <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM için dinler) gibi) `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` aracılığıyla tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="5e029-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır. <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*></span><span class="sxs-lookup"><span data-stu-id="5e029-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="5e029-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="5e029-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="5e029-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="5e029-514">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="5e029-514">External control</span></span>

<span data-ttu-id="5e029-515">Ana bilgisayar dış kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="5e029-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5e029-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="5e029-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5e029-517">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="5e029-518">IHostingEnvironment arayüzü</span><span class="sxs-lookup"><span data-stu-id="5e029-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="5e029-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="5e029-520">Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="5e029-521">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5e029-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="5e029-522">IApplicationLifetime arayüzü</span><span class="sxs-lookup"><span data-stu-id="5e029-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="5e029-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>zarif kapatma istekleri de dahil olmak üzere, başlatma ve kapatma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="5e029-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="5e029-524">Arabirimdeki üç özellik, başlatma ve <xref:System.Action> kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="5e029-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="5e029-525">İptal Jetonu</span><span class="sxs-lookup"><span data-stu-id="5e029-525">Cancellation Token</span></span> | <span data-ttu-id="5e029-526">&#8230; tetiklenir</span><span class="sxs-lookup"><span data-stu-id="5e029-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="5e029-527">Ev sahibi tamamen başladı.</span><span class="sxs-lookup"><span data-stu-id="5e029-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="5e029-528">Ev sahibi zarif bir kapatma tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="5e029-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="5e029-529">Tüm istekler işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-529">All requests should be processed.</span></span> <span data-ttu-id="5e029-530">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="5e029-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="5e029-531">Ev sahibi zarif bir kapatma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="5e029-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="5e029-532">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-532">Requests may still be processing.</span></span> <span data-ttu-id="5e029-533">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="5e029-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="5e029-534">Yapıcı herhangi bir <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> sınıfa hizmet enjekte.</span><span class="sxs-lookup"><span data-stu-id="5e029-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="5e029-535">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) olayları kaydetmek için `LifetimeEventsHostedService` bir <xref:Microsoft.Extensions.Hosting.IHostedService> sınıf (bir uygulama) içine yapıcı enjeksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="5e029-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e029-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="5e029-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını talep ediyor.</span><span class="sxs-lookup"><span data-stu-id="5e029-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="5e029-538">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="5e029-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="5e029-539">Core şablonlarının ASP.NET .NET Core Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5e029-539">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="5e029-540">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="5e029-540">Host definition</span></span>

<span data-ttu-id="5e029-541">*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:</span><span class="sxs-lookup"><span data-stu-id="5e029-541">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5e029-542">Bağımlılık enjeksiyonu (DI)</span><span class="sxs-lookup"><span data-stu-id="5e029-542">Dependency injection (DI)</span></span>
* <span data-ttu-id="5e029-543">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="5e029-543">Logging</span></span>
* <span data-ttu-id="5e029-544">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5e029-544">Configuration</span></span>
* <span data-ttu-id="5e029-545">`IHostedService`Uygulama</span><span class="sxs-lookup"><span data-stu-id="5e029-545">`IHostedService` implementations</span></span>

<span data-ttu-id="5e029-546">Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="5e029-546">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="5e029-547">Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-547">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5e029-548">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="5e029-548">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5e029-549">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="5e029-549">Set up a host</span></span>

<span data-ttu-id="5e029-550">Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-550">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5e029-551">Yöntem: `Main`</span><span class="sxs-lookup"><span data-stu-id="5e029-551">The `Main` method:</span></span>

* <span data-ttu-id="5e029-552">Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="5e029-552">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5e029-553">Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.</span><span class="sxs-lookup"><span data-stu-id="5e029-553">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5e029-554">ASP.NET Core web şablonları bir ana bilgisayar oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5e029-554">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="5e029-555">Aşağıdaki kod, DI kapsayıcısına eklenen `IHostedService` bir uygulamayla HTTP dışı bir iş yükü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5e029-555">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="5e029-556">Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:</span><span class="sxs-lookup"><span data-stu-id="5e029-556">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5e029-557">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="5e029-557">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5e029-558">[Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-558">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5e029-559">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="5e029-559">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5e029-560">Varsayılan oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="5e029-560">Default builder settings</span></span>

<span data-ttu-id="5e029-561">Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="5e029-561">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="5e029-562">İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-562">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="5e029-563">Yükler ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5e029-563">Loads host configuration from:</span></span>
  * <span data-ttu-id="5e029-564">Çevre değişkenleri . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="5e029-564">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5e029-565">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-565">Command-line arguments.</span></span>
* <span data-ttu-id="5e029-566">Uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5e029-566">Loads app configuration from:</span></span>
  * <span data-ttu-id="5e029-567">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5e029-567">*appsettings.json*.</span></span>
  * <span data-ttu-id="5e029-568">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="5e029-568">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5e029-569">[Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="5e029-569">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5e029-570">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-570">Environment variables.</span></span>
  * <span data-ttu-id="5e029-571">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5e029-571">Command-line arguments.</span></span>
* <span data-ttu-id="5e029-572">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="5e029-572">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5e029-573">Konsol</span><span class="sxs-lookup"><span data-stu-id="5e029-573">Console</span></span>
  * <span data-ttu-id="5e029-574">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="5e029-574">Debug</span></span>
  * <span data-ttu-id="5e029-575">EventSource</span><span class="sxs-lookup"><span data-stu-id="5e029-575">EventSource</span></span>
  * <span data-ttu-id="5e029-576">EventLog (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="5e029-576">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5e029-577">Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)</span><span class="sxs-lookup"><span data-stu-id="5e029-577">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5e029-578">Yöntem: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="5e029-578">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5e029-579">`ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.</span><span class="sxs-lookup"><span data-stu-id="5e029-579">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5e029-580">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5e029-580">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5e029-581">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-581">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5e029-582">[Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-582">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5e029-583">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5e029-584">IIS tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-584">Enables IIS integration.</span></span> <span data-ttu-id="5e029-585">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-585">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5e029-586">Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-586">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5e029-587">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="5e029-587">Framework-provided services</span></span>

<span data-ttu-id="5e029-588">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5e029-588">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5e029-589">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5e029-589">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5e029-590">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5e029-590">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5e029-591">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5e029-591">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5e029-592">Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-592">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5e029-593">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5e029-593">IHostApplicationLifetime</span></span>

<span data-ttu-id="5e029-594">Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="5e029-594">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5e029-595">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="5e029-595">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5e029-596">Arabirim de `StopApplication` bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="5e029-596">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5e029-597">Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="5e029-597">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5e029-598">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5e029-598">IHostLifetime</span></span>

<span data-ttu-id="5e029-599">Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler.</span><span class="sxs-lookup"><span data-stu-id="5e029-599">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5e029-600">Son kayıtlı uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-600">The last implementation registered is used.</span></span>

<span data-ttu-id="5e029-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="5e029-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5e029-602">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5e029-602">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5e029-603"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="5e029-603">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="5e029-604">[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="5e029-604">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5e029-605">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5e029-605">IHostEnvironment</span></span>

<span data-ttu-id="5e029-606">Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="5e029-606">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5e029-607">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5e029-607">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5e029-608">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5e029-608">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5e029-609">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5e029-609">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="5e029-610">Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="5e029-610">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5e029-611">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5e029-611">Host configuration</span></span>

<span data-ttu-id="5e029-612">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-612">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5e029-613">Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-613">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="5e029-614">Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-614">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5e029-615">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın.</span><span class="sxs-lookup"><span data-stu-id="5e029-615">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5e029-616">`ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-616">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5e029-617">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-617">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5e029-618">Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="5e029-618">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5e029-619">Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-619">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5e029-620">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-620">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5e029-621">Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="5e029-621">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5e029-622">Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5e029-622">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5e029-623">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5e029-623">App configuration</span></span>

<span data-ttu-id="5e029-624">Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5e029-624">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5e029-625">`ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-625">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5e029-626">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-626">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5e029-627">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-627">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5e029-628">Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-628">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5e029-629">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.</span><span class="sxs-lookup"><span data-stu-id="5e029-629">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5e029-630">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5e029-630">Settings for all app types</span></span>

<span data-ttu-id="5e029-631">Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-631">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5e029-632">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-632">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5e029-633">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5e029-633">ApplicationName</span></span>

<span data-ttu-id="5e029-634">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5e029-634">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5e029-635">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="5e029-635">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5e029-636">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-636">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-637">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="5e029-637">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5e029-638">**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5e029-638">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5e029-639">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5e029-639">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="5e029-640">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="5e029-640">ContentRoot</span></span>

<span data-ttu-id="5e029-641">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-641">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5e029-642">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="5e029-642">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5e029-643">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5e029-643">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5e029-644">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-644">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-645">**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="5e029-645">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5e029-646">**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5e029-646">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5e029-647">Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5e029-647">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5e029-648">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-648">For more information, see:</span></span>

* [<span data-ttu-id="5e029-649">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="5e029-649">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5e029-650">Webroot</span><span class="sxs-lookup"><span data-stu-id="5e029-650">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5e029-651">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5e029-651">EnvironmentName</span></span>

<span data-ttu-id="5e029-652">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-652">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5e029-653">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="5e029-653">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5e029-654">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5e029-654">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5e029-655">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="5e029-655">**Key**: `environment`</span></span>  
<span data-ttu-id="5e029-656">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-656">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-657">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="5e029-657">**Default**: `Production`</span></span>  
<span data-ttu-id="5e029-658">**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5e029-658">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5e029-659">Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5e029-659">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5e029-660">KapatmaTimeout</span><span class="sxs-lookup"><span data-stu-id="5e029-660">ShutdownTimeout</span></span>

<span data-ttu-id="5e029-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5e029-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5e029-662">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5e029-662">The default value is five seconds.</span></span>  <span data-ttu-id="5e029-663">Zaman açılığı dönüşü nde, ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="5e029-663">During the timeout period, the host:</span></span>

* <span data-ttu-id="5e029-664">Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5e029-664">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5e029-665">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.</span><span class="sxs-lookup"><span data-stu-id="5e029-665">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5e029-666">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="5e029-666">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5e029-667">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="5e029-667">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5e029-668">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="5e029-668">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5e029-669">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5e029-669">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5e029-670">**Türü**:`int`</span><span class="sxs-lookup"><span data-stu-id="5e029-670">**Type**: `int`</span></span>  
<span data-ttu-id="5e029-671">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="5e029-671">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5e029-672">**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5e029-672">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5e029-673">Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions`</span><span class="sxs-lookup"><span data-stu-id="5e029-673">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5e029-674">Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5e029-674">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="5e029-675">Değişiklikte uygulama yapılandırması yeniden yüklemeyi devre dışı kaldırma</span><span class="sxs-lookup"><span data-stu-id="5e029-675">Disable app configuration reload on change</span></span>

<span data-ttu-id="5e029-676">[Varsayılan olarak,](xref:fundamentals/configuration/index#default) *appsettings.json* ve *appsettings.{ Dosya değiştiğinde Environment}.json* yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-676">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="5e029-677">Bu yeniden yükleme davranışını ASP.NET Core 5.0 Preview 3 `hostBuilder:reloadConfigOnChange` veya `false`sonraki ASP.NET devre dışı bırakabilirsiniz, anahtarı .</span><span class="sxs-lookup"><span data-stu-id="5e029-677">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="5e029-678">**Anahtar**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5e029-678">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5e029-679">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-679">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-680">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="5e029-680">**Default**: `true`</span></span>  
<span data-ttu-id="5e029-681">**Komut satırı bağımsız değişkeni**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5e029-681">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5e029-682">**Çevre değişkeni**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5e029-682">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="5e029-683">İki nokta`:`üst üste ( ) ayırıcı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="5e029-683">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="5e029-684">Daha fazla bilgi için [Bkz. Çevre değişkenleri.](xref:fundamentals/configuration/index#environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5e029-684">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="5e029-685">Web uygulamaları için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5e029-685">Settings for web apps</span></span>

<span data-ttu-id="5e029-686">Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-686">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5e029-687">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-687">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5e029-688">Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="5e029-688">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5e029-689">Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="5e029-689">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5e029-690">YakalamaBaşlangıç Hataları</span><span class="sxs-lookup"><span data-stu-id="5e029-690">CaptureStartupErrors</span></span>

<span data-ttu-id="5e029-691">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="5e029-691">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5e029-692">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="5e029-692">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5e029-693">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5e029-693">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5e029-694">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-694">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-695">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="5e029-695">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5e029-696">**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5e029-696">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5e029-697">Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-697">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5e029-698">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="5e029-698">DetailedErrors</span></span>

<span data-ttu-id="5e029-699">Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="5e029-699">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5e029-700">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5e029-700">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5e029-701">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-701">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-702">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5e029-702">**Default**: `false`</span></span>  
<span data-ttu-id="5e029-703">**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5e029-703">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5e029-704">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-704">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5e029-705">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5e029-705">HostingStartupAssemblies</span></span>

<span data-ttu-id="5e029-706">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5e029-706">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5e029-707">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="5e029-707">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5e029-708">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-708">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5e029-709">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5e029-709">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5e029-710">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-710">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-711">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5e029-711">**Default**: Empty string</span></span>  
<span data-ttu-id="5e029-712">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5e029-712">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5e029-713">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-713">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5e029-714">HostingStartupExcludeMontajies</span><span class="sxs-lookup"><span data-stu-id="5e029-714">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5e029-715">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5e029-715">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5e029-716">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5e029-716">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5e029-717">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-717">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-718">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5e029-718">**Default**: Empty string</span></span>  
<span data-ttu-id="5e029-719">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5e029-719">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5e029-720">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-720">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5e029-721">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5e029-721">HTTPS_Port</span></span>

<span data-ttu-id="5e029-722">HTTPS yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="5e029-722">The HTTPS redirect port.</span></span> <span data-ttu-id="5e029-723">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-723">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5e029-724">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="5e029-724">**Key**: `https_port`</span></span>  
<span data-ttu-id="5e029-725">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-725">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-726">**Varsayılan**: Varsayılan değer ayarlı değil.</span><span class="sxs-lookup"><span data-stu-id="5e029-726">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5e029-727">**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5e029-727">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5e029-728">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5e029-728">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5e029-729">TercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5e029-729">PreferHostingUrls</span></span>

<span data-ttu-id="5e029-730">Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-730">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5e029-731">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5e029-731">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5e029-732">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-732">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-733">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="5e029-733">**Default**: `true`</span></span>  
<span data-ttu-id="5e029-734">**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5e029-734">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5e029-735">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5e029-735">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5e029-736">HostingBaşlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="5e029-736">PreventHostingStartup</span></span>

<span data-ttu-id="5e029-737">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="5e029-737">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5e029-738">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5e029-738">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5e029-739">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5e029-739">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5e029-740">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5e029-740">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5e029-741">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5e029-741">**Default**: `false`</span></span>  
<span data-ttu-id="5e029-742">**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5e029-742">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5e029-743">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5e029-743">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5e029-744">Başlangıç Montaj</span><span class="sxs-lookup"><span data-stu-id="5e029-744">StartupAssembly</span></span>

<span data-ttu-id="5e029-745">`Startup` Sınıfı aramak için derleme.</span><span class="sxs-lookup"><span data-stu-id="5e029-745">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5e029-746">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5e029-746">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5e029-747">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-747">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-748">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="5e029-748">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5e029-749">**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5e029-749">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5e029-750">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="5e029-750">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5e029-751">`UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir).</span><span class="sxs-lookup"><span data-stu-id="5e029-751">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5e029-752">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-752">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5e029-753">URL’ler</span><span class="sxs-lookup"><span data-stu-id="5e029-753">URLs</span></span>

<span data-ttu-id="5e029-754">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi.</span><span class="sxs-lookup"><span data-stu-id="5e029-754">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5e029-755">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5e029-755">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5e029-756">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="5e029-756">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5e029-757">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5e029-757">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5e029-758">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="5e029-758">Supported formats vary among servers.</span></span>

<span data-ttu-id="5e029-759">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="5e029-759">**Key**: `urls`</span></span>  
<span data-ttu-id="5e029-760">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-760">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-761">**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5e029-761">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5e029-762">**Çevre değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5e029-762">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5e029-763">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="5e029-763">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5e029-764">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="5e029-764">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5e029-765">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5e029-765">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5e029-766">Webroot</span><span class="sxs-lookup"><span data-stu-id="5e029-766">WebRoot</span></span>

<span data-ttu-id="5e029-767">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği uygulamanın statik varlıklarıiçin göreli yolu belirler.</span><span class="sxs-lookup"><span data-stu-id="5e029-767">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="5e029-768">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e029-768">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="5e029-769">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="5e029-769">**Key**: `webroot`</span></span>  
<span data-ttu-id="5e029-770">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5e029-770">**Type**: `string`</span></span>  
<span data-ttu-id="5e029-771">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="5e029-771">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5e029-772">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5e029-772">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="5e029-773">**Çevre değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5e029-773">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5e029-774">Bu değeri ayarlamak için, ortam `UseWebRoot` değişkenini kullanın veya çağırın: `IWebHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5e029-774">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5e029-775">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e029-775">For more information, see:</span></span>

* [<span data-ttu-id="5e029-776">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="5e029-776">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5e029-777">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="5e029-777">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5e029-778">Ev sahibinin ömrünü yönetin</span><span class="sxs-lookup"><span data-stu-id="5e029-778">Manage the host lifetime</span></span>

<span data-ttu-id="5e029-779">Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın.</span><span class="sxs-lookup"><span data-stu-id="5e029-779">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5e029-780">Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="5e029-780">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5e029-781">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5e029-781">Run</span></span>

<span data-ttu-id="5e029-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5e029-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5e029-783">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-783">RunAsync</span></span>

<span data-ttu-id="5e029-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.</span><span class="sxs-lookup"><span data-stu-id="5e029-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5e029-785">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-785">RunConsoleAsync</span></span>

<span data-ttu-id="5e029-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="5e029-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5e029-787">Başlat</span><span class="sxs-lookup"><span data-stu-id="5e029-787">Start</span></span>

<span data-ttu-id="5e029-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="5e029-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5e029-789">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-789">StartAsync</span></span>

<span data-ttu-id="5e029-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir.</span><span class="sxs-lookup"><span data-stu-id="5e029-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5e029-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="5e029-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5e029-792">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5e029-792">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5e029-793">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-793">StopAsync</span></span>

<span data-ttu-id="5e029-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5e029-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5e029-795">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="5e029-795">WaitForShutdown</span></span>

<span data-ttu-id="5e029-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.</span><span class="sxs-lookup"><span data-stu-id="5e029-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5e029-797">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5e029-797">WaitForShutdownAsync</span></span>

<span data-ttu-id="5e029-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="5e029-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5e029-799">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="5e029-799">External control</span></span>

<span data-ttu-id="5e029-800">Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="5e029-800">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5e029-801">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5e029-801">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
