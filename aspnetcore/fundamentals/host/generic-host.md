---
title: .NET Genel Ana Bilgisayar
author: rick-anderson
description: Uygulama başlatma ve yaşam boyu yönetimden sorumlu .NET Core Generic Host hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488780"
---
# <a name="net-generic-host"></a><span data-ttu-id="4c204-103">.NET Genel Ana Bilgisayar</span><span class="sxs-lookup"><span data-stu-id="4c204-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="4c204-104">ASP.NET Core şablonları bir .NET Core <xref:Microsoft.Extensions.Hosting.HostBuilder>Genel Ana Bilgisayar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4c204-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="4c204-105">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="4c204-105">Host definition</span></span>

<span data-ttu-id="4c204-106">*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:</span><span class="sxs-lookup"><span data-stu-id="4c204-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4c204-107">Bağımlılık enjeksiyonu (DI)</span><span class="sxs-lookup"><span data-stu-id="4c204-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="4c204-108">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="4c204-108">Logging</span></span>
* <span data-ttu-id="4c204-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4c204-109">Configuration</span></span>
* <span data-ttu-id="4c204-110">`IHostedService`Uygulama</span><span class="sxs-lookup"><span data-stu-id="4c204-110">`IHostedService` implementations</span></span>

<span data-ttu-id="4c204-111">Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="4c204-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="4c204-112">Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4c204-113">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="4c204-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4c204-114">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="4c204-114">Set up a host</span></span>

<span data-ttu-id="4c204-115">Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4c204-116">Yöntem: `Main`</span><span class="sxs-lookup"><span data-stu-id="4c204-116">The `Main` method:</span></span>

* <span data-ttu-id="4c204-117">Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="4c204-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4c204-118">Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.</span><span class="sxs-lookup"><span data-stu-id="4c204-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4c204-119">ASP.NET Core web şablonları, Genel Ana Bilgisayar oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4c204-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="4c204-120">Aşağıdaki kod, HTTP olmayan iş yükünü kullanarak genel ana bilgisayar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4c204-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="4c204-121">Uygulama `IHostedService` DI kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="4c204-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="4c204-122">Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:</span><span class="sxs-lookup"><span data-stu-id="4c204-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4c204-123">Önceki kod ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4c204-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="4c204-124">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="4c204-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4c204-125">[Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4c204-126">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="4c204-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4c204-127">Varsayılan oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="4c204-127">Default builder settings</span></span>

<span data-ttu-id="4c204-128">Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="4c204-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4c204-129">İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4c204-130">Yükler ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="4c204-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="4c204-131">Çevre değişkenleri . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="4c204-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4c204-132">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-132">Command-line arguments.</span></span>
* <span data-ttu-id="4c204-133">Uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="4c204-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="4c204-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4c204-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="4c204-135">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="4c204-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4c204-136">[Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="4c204-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4c204-137">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-137">Environment variables.</span></span>
  * <span data-ttu-id="4c204-138">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-138">Command-line arguments.</span></span>
* <span data-ttu-id="4c204-139">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="4c204-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4c204-140">Konsol</span><span class="sxs-lookup"><span data-stu-id="4c204-140">Console</span></span>
  * <span data-ttu-id="4c204-141">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="4c204-141">Debug</span></span>
  * <span data-ttu-id="4c204-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="4c204-142">EventSource</span></span>
  * <span data-ttu-id="4c204-143">EventLog (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="4c204-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4c204-144">Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)</span><span class="sxs-lookup"><span data-stu-id="4c204-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4c204-145">Yöntem: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="4c204-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4c204-146">`ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.</span><span class="sxs-lookup"><span data-stu-id="4c204-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4c204-147">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4c204-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4c204-148">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4c204-149">[Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4c204-150">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4c204-151">IIS tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-151">Enables IIS integration.</span></span> <span data-ttu-id="4c204-152">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4c204-153">Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4c204-154">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="4c204-154">Framework-provided services</span></span>

<span data-ttu-id="4c204-155">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="4c204-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4c204-156">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="4c204-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4c204-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4c204-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4c204-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4c204-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4c204-159">Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4c204-160">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="4c204-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="4c204-161">Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="4c204-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4c204-162">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="4c204-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4c204-163">Arabirim de `StopApplication` bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="4c204-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4c204-164">Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="4c204-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4c204-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4c204-165">IHostLifetime</span></span>

<span data-ttu-id="4c204-166">Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler.</span><span class="sxs-lookup"><span data-stu-id="4c204-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4c204-167">Son kayıtlı uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-167">The last implementation registered is used.</span></span>

<span data-ttu-id="4c204-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="4c204-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4c204-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4c204-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4c204-170"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="4c204-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4c204-171">[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="4c204-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4c204-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4c204-172">IHostEnvironment</span></span>

<span data-ttu-id="4c204-173">Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="4c204-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4c204-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4c204-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4c204-175">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="4c204-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4c204-176">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="4c204-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4c204-177">Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="4c204-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4c204-178">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4c204-178">Host configuration</span></span>

<span data-ttu-id="4c204-179">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4c204-180">Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4c204-181">Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4c204-182">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın.</span><span class="sxs-lookup"><span data-stu-id="4c204-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4c204-183">`ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4c204-184">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4c204-185">Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="4c204-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4c204-186">Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4c204-187">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4c204-188">Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="4c204-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4c204-189">Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4c204-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4c204-190">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4c204-190">App configuration</span></span>

<span data-ttu-id="4c204-191">Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4c204-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4c204-192">`ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4c204-193">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4c204-194">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4c204-195">Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4c204-196">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.</span><span class="sxs-lookup"><span data-stu-id="4c204-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4c204-197">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="4c204-197">Settings for all app types</span></span>

<span data-ttu-id="4c204-198">Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4c204-199">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4c204-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4c204-200">ApplicationName</span></span>

<span data-ttu-id="4c204-201">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4c204-202">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4c204-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4c204-203">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-203">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-204">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="4c204-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4c204-205">**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4c204-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4c204-206">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c204-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4c204-207">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="4c204-207">ContentRoot</span></span>

<span data-ttu-id="4c204-208">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4c204-209">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="4c204-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4c204-210">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4c204-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4c204-211">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-211">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-212">**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="4c204-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4c204-213">**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4c204-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4c204-214">Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="4c204-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4c204-215">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-215">For more information, see:</span></span>

* [<span data-ttu-id="4c204-216">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="4c204-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4c204-217">Webroot</span><span class="sxs-lookup"><span data-stu-id="4c204-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4c204-218">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="4c204-218">EnvironmentName</span></span>

<span data-ttu-id="4c204-219">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4c204-220">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="4c204-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4c204-221">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="4c204-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4c204-222">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="4c204-222">**Key**: `environment`</span></span>  
<span data-ttu-id="4c204-223">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-223">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-224">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="4c204-224">**Default**: `Production`</span></span>  
<span data-ttu-id="4c204-225">**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4c204-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4c204-226">Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="4c204-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4c204-227">KapatmaTimeout</span><span class="sxs-lookup"><span data-stu-id="4c204-227">ShutdownTimeout</span></span>

<span data-ttu-id="4c204-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4c204-229">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="4c204-229">The default value is five seconds.</span></span>  <span data-ttu-id="4c204-230">Zaman açılığı dönüşü nde, ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="4c204-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="4c204-231">Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4c204-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4c204-232">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.</span><span class="sxs-lookup"><span data-stu-id="4c204-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4c204-233">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="4c204-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4c204-234">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="4c204-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4c204-235">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="4c204-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4c204-236">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4c204-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4c204-237">**Türü**:`int`</span><span class="sxs-lookup"><span data-stu-id="4c204-237">**Type**: `int`</span></span>  
<span data-ttu-id="4c204-238">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="4c204-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4c204-239">**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4c204-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4c204-240">Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions`</span><span class="sxs-lookup"><span data-stu-id="4c204-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4c204-241">Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:</span><span class="sxs-lookup"><span data-stu-id="4c204-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="4c204-242">Web uygulamaları için ayarlar</span><span class="sxs-lookup"><span data-stu-id="4c204-242">Settings for web apps</span></span>

<span data-ttu-id="4c204-243">Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4c204-244">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4c204-245">Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="4c204-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4c204-246">Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="4c204-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4c204-247">YakalamaBaşlangıç Hataları</span><span class="sxs-lookup"><span data-stu-id="4c204-247">CaptureStartupErrors</span></span>

<span data-ttu-id="4c204-248">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="4c204-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4c204-249">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="4c204-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4c204-250">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4c204-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4c204-251">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-252">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="4c204-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4c204-253">**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4c204-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4c204-254">Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4c204-255">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="4c204-255">DetailedErrors</span></span>

<span data-ttu-id="4c204-256">Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="4c204-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4c204-257">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4c204-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4c204-258">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-259">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="4c204-259">**Default**: `false`</span></span>  
<span data-ttu-id="4c204-260">**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4c204-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4c204-261">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4c204-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4c204-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="4c204-263">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="4c204-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4c204-264">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="4c204-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4c204-265">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4c204-266">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4c204-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4c204-267">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-267">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-268">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="4c204-268">**Default**: Empty string</span></span>  
<span data-ttu-id="4c204-269">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4c204-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4c204-270">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4c204-271">HostingStartupExcludeMontajies</span><span class="sxs-lookup"><span data-stu-id="4c204-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4c204-272">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="4c204-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4c204-273">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4c204-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4c204-274">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-274">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-275">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="4c204-275">**Default**: Empty string</span></span>  
<span data-ttu-id="4c204-276">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4c204-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4c204-277">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4c204-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4c204-278">HTTPS_Port</span></span>

<span data-ttu-id="4c204-279">HTTPS yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="4c204-279">The HTTPS redirect port.</span></span> <span data-ttu-id="4c204-280">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4c204-281">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="4c204-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="4c204-282">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-282">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-283">**Varsayılan**: Varsayılan değer ayarlı değil.</span><span class="sxs-lookup"><span data-stu-id="4c204-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4c204-284">**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4c204-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4c204-285">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4c204-286">TercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4c204-286">PreferHostingUrls</span></span>

<span data-ttu-id="4c204-287">Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4c204-288">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4c204-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4c204-289">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-290">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="4c204-290">**Default**: `true`</span></span>  
<span data-ttu-id="4c204-291">**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4c204-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4c204-292">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4c204-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4c204-293">HostingBaşlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="4c204-293">PreventHostingStartup</span></span>

<span data-ttu-id="4c204-294">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4c204-295">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4c204-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4c204-296">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4c204-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4c204-297">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-298">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="4c204-298">**Default**: `false`</span></span>  
<span data-ttu-id="4c204-299">**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4c204-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4c204-300">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4c204-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4c204-301">Başlangıç Montaj</span><span class="sxs-lookup"><span data-stu-id="4c204-301">StartupAssembly</span></span>

<span data-ttu-id="4c204-302">`Startup` Sınıfı aramak için derleme.</span><span class="sxs-lookup"><span data-stu-id="4c204-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4c204-303">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4c204-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4c204-304">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-304">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-305">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="4c204-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4c204-306">**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4c204-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4c204-307">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="4c204-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4c204-308">`UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir).</span><span class="sxs-lookup"><span data-stu-id="4c204-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4c204-309">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4c204-310">URL’ler</span><span class="sxs-lookup"><span data-stu-id="4c204-310">URLs</span></span>

<span data-ttu-id="4c204-311">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi.</span><span class="sxs-lookup"><span data-stu-id="4c204-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4c204-312">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4c204-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4c204-313">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c204-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4c204-314">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4c204-315">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="4c204-316">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="4c204-316">**Key**: `urls`</span></span>  
<span data-ttu-id="4c204-317">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-317">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-318">**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4c204-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4c204-319">**Çevre değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4c204-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4c204-320">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="4c204-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4c204-321">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="4c204-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4c204-322">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4c204-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4c204-323">Webroot</span><span class="sxs-lookup"><span data-stu-id="4c204-323">WebRoot</span></span>

<span data-ttu-id="4c204-324">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği uygulamanın statik varlıklarıiçin göreli yolu belirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4c204-325">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4c204-326">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="4c204-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="4c204-327">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-327">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-328">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="4c204-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4c204-329">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4c204-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4c204-330">**Çevre değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4c204-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4c204-331">Bu değeri ayarlamak için, ortam `UseWebRoot` değişkenini kullanın veya çağırın: `IWebHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="4c204-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4c204-332">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-332">For more information, see:</span></span>

* [<span data-ttu-id="4c204-333">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="4c204-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4c204-334">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="4c204-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4c204-335">Ev sahibinin ömrünü yönetin</span><span class="sxs-lookup"><span data-stu-id="4c204-335">Manage the host lifetime</span></span>

<span data-ttu-id="4c204-336">Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın.</span><span class="sxs-lookup"><span data-stu-id="4c204-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4c204-337">Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="4c204-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4c204-338">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="4c204-338">Run</span></span>

<span data-ttu-id="4c204-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="4c204-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4c204-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-340">RunAsync</span></span>

<span data-ttu-id="4c204-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.</span><span class="sxs-lookup"><span data-stu-id="4c204-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4c204-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-342">RunConsoleAsync</span></span>

<span data-ttu-id="4c204-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4c204-344">Başlat</span><span class="sxs-lookup"><span data-stu-id="4c204-344">Start</span></span>

<span data-ttu-id="4c204-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="4c204-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4c204-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-346">StartAsync</span></span>

<span data-ttu-id="4c204-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir.</span><span class="sxs-lookup"><span data-stu-id="4c204-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4c204-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="4c204-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4c204-349">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4c204-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-350">StopAsync</span></span>

<span data-ttu-id="4c204-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4c204-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4c204-352">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="4c204-352">WaitForShutdown</span></span>

<span data-ttu-id="4c204-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.</span><span class="sxs-lookup"><span data-stu-id="4c204-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4c204-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="4c204-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="4c204-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4c204-356">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="4c204-356">External control</span></span>

<span data-ttu-id="4c204-357">Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="4c204-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="4c204-358">ASP.NET Core uygulamaları bir ana bilgisayar yapılandırır ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="4c204-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="4c204-359">Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="4c204-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="4c204-360">Bu makale, HTTP isteklerini<xref:Microsoft.Extensions.Hosting.HostBuilder>işlemeyan uygulamalar için kullanılan ASP.NET Core Genel Ana Bilgisayar (),</span><span class="sxs-lookup"><span data-stu-id="4c204-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="4c204-361">Genel Ana Bilgisayar'ın amacı, daha geniş bir ana bilgisayar senaryosu dizisi sağlamak için HTTP ardışık hattını Web Ana Bilgisayar API'sinden ayırmaktır.</span><span class="sxs-lookup"><span data-stu-id="4c204-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="4c204-362">Genel Ana Bilgisayar'a dayalı mesajlaşma, arka plan görevleri ve diğer HTTP dışındaki iş yükleri yapılandırma, bağımlılık enjeksiyonu (DI) ve günlüğe kaydetme gibi çapraz kesme özelliklerinden yararlanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="4c204-363">Genel Ana Bilgisayar, ASP.NET Core 2.1'de yenidir ve web barındırma senaryoları için uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="4c204-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="4c204-364">Web barındırma senaryoları için [Web Barındıran Alanı'nı](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c204-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4c204-365">Genel Ana Bilgisayar, gelecekteki bir sürümde Web Host'un yerini alır ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API'si olarak hareket eder.</span><span class="sxs-lookup"><span data-stu-id="4c204-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="4c204-366">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c204-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4c204-367">Örnek uygulamayı Visual [Studio Code'da](https://code.visualstudio.com/)çalıştırırken *harici veya entegre*bir terminal kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c204-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="4c204-368">Örneği bir `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="4c204-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="4c204-369">Konsolu Visual Studio Code'da ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="4c204-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="4c204-370">*.vscode/launch.json* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="4c204-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="4c204-371">**.NET Core Launch (konsol)** yapılandırmasında, **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="4c204-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="4c204-372">Değeri ya da `externalTerminal` `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="4c204-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="4c204-373">Giriş</span><span class="sxs-lookup"><span data-stu-id="4c204-373">Introduction</span></span>

<span data-ttu-id="4c204-374">Genel Ana Bilgisayar kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="4c204-375">Paket `Microsoft.Extensions.Hosting` [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (core 2.1 veya daha sonra ASP.NET) dahildir.</span><span class="sxs-lookup"><span data-stu-id="4c204-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="4c204-376"><xref:Microsoft.Extensions.Hosting.IHostedService>kod yürütmeiçin giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="4c204-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="4c204-377">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama [ConfigureServices hizmet kaydı](#configureservices)sırasına göre yürütülür.</span><span class="sxs-lookup"><span data-stu-id="4c204-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="4c204-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>ana bilgisayar <xref:Microsoft.Extensions.Hosting.IHostedService> başladığında her birine <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> çağrılır ve ana bilgisayar zarif bir şekilde kapandığında ters kayıt sırasına çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4c204-379">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="4c204-379">Set up a host</span></span>

<span data-ttu-id="4c204-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>kitaplıkların ve uygulamaların ana bilgisayarı başlatma, oluşturmak ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="4c204-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="4c204-381">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4c204-381">Options</span></span>

<span data-ttu-id="4c204-382"><xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırmak <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="4c204-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4c204-383">Kapatma zaman</span><span class="sxs-lookup"><span data-stu-id="4c204-383">Shutdown timeout</span></span>

<span data-ttu-id="4c204-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4c204-385">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="4c204-385">The default value is five seconds.</span></span>

<span data-ttu-id="4c204-386">Aşağıdaki seçenek yapılandırmasında `Program.Main` varsayılan beş saniyelik kapatma zaman dışarısını 20 saniyeye çıkarır:</span><span class="sxs-lookup"><span data-stu-id="4c204-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="4c204-387">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="4c204-387">Default services</span></span>

<span data-ttu-id="4c204-388">Aşağıdaki hizmetler ana bilgisayar başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="4c204-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="4c204-389">[Çevre](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="4c204-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="4c204-390">[Yapılandırma](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="4c204-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="4c204-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4c204-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="4c204-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4c204-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="4c204-393">[Seçenekler](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="4c204-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="4c204-394">[Günlük](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="4c204-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4c204-395">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4c204-395">Host configuration</span></span>

<span data-ttu-id="4c204-396">Ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="4c204-396">Host configuration is created by:</span></span>

* <span data-ttu-id="4c204-397">İçerik <xref:Microsoft.Extensions.Hosting.IHostBuilder> [kökünü](#content-root) ve [ortamını](#environment)ayarlamak için uzantı yöntemlerini çağırma.</span><span class="sxs-lookup"><span data-stu-id="4c204-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="4c204-398">Yapılandırma sağlayıcılarından yapılandırmayı <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>okuma.</span><span class="sxs-lookup"><span data-stu-id="4c204-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="4c204-399">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="4c204-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4c204-400">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="4c204-400">Application key (name)</span></span>

<span data-ttu-id="4c204-401">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği, ana bilgisayar inşaatı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="4c204-402">Değeri açıkça ayarlamak için [HostDefaults.ApplicationKey:'i](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="4c204-403">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4c204-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4c204-404">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-404">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-405">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="4c204-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4c204-406">**Set kullanarak:**`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="4c204-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="4c204-407">**Çevre değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4c204-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="4c204-408">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="4c204-408">Content root</span></span>

<span data-ttu-id="4c204-409">Bu ayar, ana bilgisayarın içerik dosyalarını aramaya nerede başlayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="4c204-410">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4c204-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4c204-411">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-411">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-412">**Varsayılan**: Varsayılan olarak uygulama derlemesinin bulunduğu klasöre iner.</span><span class="sxs-lookup"><span data-stu-id="4c204-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4c204-413">**Set kullanarak:**`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4c204-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4c204-414">**Çevre değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4c204-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4c204-415">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="4c204-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="4c204-416">Daha fazla bilgi için [temel bilgiler: İçerik kökü.](xref:fundamentals/index#content-root)</span><span class="sxs-lookup"><span data-stu-id="4c204-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="4c204-417">Ortam</span><span class="sxs-lookup"><span data-stu-id="4c204-417">Environment</span></span>

<span data-ttu-id="4c204-418">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4c204-419">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="4c204-419">**Key**: `environment`</span></span>  
<span data-ttu-id="4c204-420">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-420">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-421">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="4c204-421">**Default**: `Production`</span></span>  
<span data-ttu-id="4c204-422">**Set kullanarak:**`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4c204-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4c204-423">**Çevre değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4c204-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4c204-424">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-424">The environment can be set to any value.</span></span> <span data-ttu-id="4c204-425">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="4c204-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4c204-426">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="4c204-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="4c204-427">YapılandırmaHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="4c204-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="4c204-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ana <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> bilgisayar için <xref:Microsoft.Extensions.Configuration.IConfiguration> bir oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="4c204-429">Ana bilgisayar yapılandırması, uygulamanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> yapı işleminde kullanım için başlatma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="4c204-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4c204-431">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4c204-432">Hiçbir sağlayıcı varsayılan olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="4c204-432">No providers are included by default.</span></span> <span data-ttu-id="4c204-433">Aşağıdakiler de dahil olmak üzere, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>uygulamanın gerektirdiği yapılandırma sağlayıcılarını açıkça belirtmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="4c204-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="4c204-434">Dosya yapılandırması (örneğin, bir *hostsettings.json* dosyasından).</span><span class="sxs-lookup"><span data-stu-id="4c204-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="4c204-435">Çevre değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="4c204-435">Environment variable configuration.</span></span>
* <span data-ttu-id="4c204-436">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="4c204-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="4c204-437">Diğer gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="4c204-437">Any other required configuration providers.</span></span>

<span data-ttu-id="4c204-438">Ana bilgisayar dosya yapılandırması, uygulamanın temel yolunu `SetBasePath` belirterek ve ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrıyla etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="4c204-439">Örnek uygulama bir JSON dosyası, *hostsettings.json*ve dosyanın ana bilgisayar yapılandırma ayarlarını tüketmek için aramalar <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="4c204-440">Ana bilgisayara [ortam değişkenyapılandırması](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için ana bilgisayar oluşturucuyu arayın. <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*></span><span class="sxs-lookup"><span data-stu-id="4c204-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="4c204-441">`AddEnvironmentVariables`isteğe bağlı kullanıcı tanımlı öneki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4c204-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="4c204-442">Örnek uygulama bir önek `PREFIX_`kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="4c204-443">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4c204-444">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, ortam `PREFIX_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="4c204-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4c204-445">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya bir `dotnet run`uygulamayı çalıştırırken geliştirme *sırasında, özellikler/launchSettings.json* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="4c204-446">[Visual Studio Code'da](https://code.visualstudio.com/)ortam değişkenleri geliştirme sırasında *.vscode/launch.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="4c204-447">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4c204-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4c204-448">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağrılar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>eklenmez.</span><span class="sxs-lookup"><span data-stu-id="4c204-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="4c204-449">Komut satırı yapılandırması, komut satırı bağımsız değişkenlerini önceki yapılandırma sağlayıcıları tarafından sağlanan yapılandırmayı geçersiz kılmak için en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="4c204-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4c204-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="4c204-451">Ek yapılandırma [applicationName](#application-key-name) ve [contentRoot](#content-root) tuşları ile sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="4c204-452">Örnek `HostBuilder` yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>kullanarak:</span><span class="sxs-lookup"><span data-stu-id="4c204-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="4c204-453">YapılandırmaAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4c204-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4c204-454">Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostBuilder> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uygulama çağırılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4c204-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="4c204-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>uygulama <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="4c204-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4c204-457">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="4c204-458">Tarafından <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) kullanılabilir ve <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="4c204-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="4c204-459">Uygulama [yapılandırması, ConfigureHostConfiguration](#configurehostconfiguration)tarafından sağlanan ana bilgisayar yapılandırması otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="4c204-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="4c204-460">Örnek uygulama <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>yapılandırması kullanarak:</span><span class="sxs-lookup"><span data-stu-id="4c204-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="4c204-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4c204-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="4c204-462">*ayarları. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="4c204-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="4c204-463">*ayarları. Üretim.json*:</span><span class="sxs-lookup"><span data-stu-id="4c204-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="4c204-464">Ayarlar dosyalarını çıktı dizinine taşımak için, proje dosyasındaki ayarlar dosyalarını [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="4c204-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="4c204-465">Örnek uygulama JSON uygulama ayarları dosyalarını ve *hostsettings.json'u* aşağıdaki `<Content>` öğeyle hareket ettirir:</span><span class="sxs-lookup"><span data-stu-id="4c204-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="4c204-466"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gibi yapılandırma uzantısı yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="4c204-467">Uygulama [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmadığı sürece, bu paketler in core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="4c204-468">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4c204-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="4c204-469">YapılandırmaHizmetleri</span><span class="sxs-lookup"><span data-stu-id="4c204-469">ConfigureServices</span></span>

<span data-ttu-id="4c204-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) konteynerine hizmetler ekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4c204-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4c204-472">Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="4c204-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="4c204-473">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4c204-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="4c204-474">[Örnek uygulama,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` `LifetimeEventsHostedService`yaşam boyu olaylar için bir hizmet ve zamanlanmış arka `TimedHostedService`plan görevi, uygulamaya eklemek için uzantı yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="4c204-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="4c204-475">YapılandırmaLogging</span><span class="sxs-lookup"><span data-stu-id="4c204-475">ConfigureLogging</span></span>

<span data-ttu-id="4c204-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>sağlanan <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırma için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="4c204-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="4c204-478">UseConsoleÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="4c204-478">UseConsoleLifetime</span></span>

<span data-ttu-id="4c204-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'i dinler ve kapatma işlemini başlatmak için çağrıda bulunur. <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*></span><span class="sxs-lookup"><span data-stu-id="4c204-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="4c204-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="4c204-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="4c204-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan ömür boyu uygulama olarak önceden kaydedilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4c204-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="4c204-482">Son ömür boyu kayıtlı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="4c204-483">Konteyner yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4c204-483">Container configuration</span></span>

<span data-ttu-id="4c204-484">Diğer kapsayıcılara takMayı desteklemek için, <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>ana bilgisayar bir .</span><span class="sxs-lookup"><span data-stu-id="4c204-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="4c204-485">Bir fabrika sağlamak DI konteyner kaydının bir parçası değildir, ancak bunun yerine beton DI konteyner oluşturmak için kullanılan bir ana bilgisayardır.</span><span class="sxs-lookup"><span data-stu-id="4c204-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="4c204-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder),&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) uygulamanın servis sağlayıcısını oluşturmak için kullanılan varsayılan fabrikayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4c204-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="4c204-487">Özel kapsayıcı yapılandırmayöntemi <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> tarafından yönetilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="4c204-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>kapsayıcıyı temel ana bilgisayar API'sinin üzerine yapılandırmak için güçlü bir şekilde yazılmamış bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="4c204-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4c204-490">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4c204-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="4c204-491">Bir servis konteyner fabrikası sağlayın:</span><span class="sxs-lookup"><span data-stu-id="4c204-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="4c204-492">Fabrikayı kullanın ve uygulama için özel servis konteynerini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="4c204-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="4c204-493">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="4c204-493">Extensibility</span></span>

<span data-ttu-id="4c204-494">Ana bilgisayar genişletilebilirliği uzantısı yöntemleri <xref:Microsoft.Extensions.Hosting.IHostBuilder>ile gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="4c204-495">Aşağıdaki örnek, uzantı yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> [timedHostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneğinde gösterildiği bir <xref:fundamentals/host/hosted-services>uygulamayı nasıl genişletir gösteriş olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="4c204-496">Bir uygulama, `UseHostedService` barındırılan hizmeti kaydetmek için `T`uzantı yöntemini belirler:</span><span class="sxs-lookup"><span data-stu-id="4c204-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="4c204-497">Ev sahibini yönetme</span><span class="sxs-lookup"><span data-stu-id="4c204-497">Manage the host</span></span>

<span data-ttu-id="4c204-498">Uygulama, <xref:Microsoft.Extensions.Hosting.IHost> hizmet konteynerine <xref:Microsoft.Extensions.Hosting.IHostedService> kayıtlı uygulamaların başlatılmasından ve durdurulmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="4c204-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4c204-499">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="4c204-499">Run</span></span>

<span data-ttu-id="4c204-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırAn ve ana bilgisayar kapatılana kadar arama iş parçacığı engeller:</span><span class="sxs-lookup"><span data-stu-id="4c204-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="4c204-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-501">RunAsync</span></span>

<span data-ttu-id="4c204-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür:</span><span class="sxs-lookup"><span data-stu-id="4c204-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="4c204-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-503">RunConsoleAsync</span></span>

<span data-ttu-id="4c204-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="4c204-505">Başlat ve DurdurAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-505">Start and StopAsync</span></span>

<span data-ttu-id="4c204-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="4c204-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="4c204-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4c204-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="4c204-508">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="4c204-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="4c204-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="4c204-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="4c204-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="4c204-511">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="4c204-511">WaitForShutdown</span></span>

<span data-ttu-id="4c204-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><xref:Microsoft.Extensions.Hosting.IHostLifetime>(Ctrl <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM için dinler) gibi) `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` aracılığıyla tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="4c204-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır. <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*></span><span class="sxs-lookup"><span data-stu-id="4c204-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="4c204-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="4c204-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="4c204-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="4c204-516">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="4c204-516">External control</span></span>

<span data-ttu-id="4c204-517">Ana bilgisayar dış kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="4c204-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="4c204-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="4c204-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4c204-519">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4c204-520">IHostingEnvironment arayüzü</span><span class="sxs-lookup"><span data-stu-id="4c204-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="4c204-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="4c204-522">Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="4c204-523">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4c204-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4c204-524">IApplicationLifetime arayüzü</span><span class="sxs-lookup"><span data-stu-id="4c204-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="4c204-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>zarif kapatma istekleri de dahil olmak üzere, başlatma ve kapatma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="4c204-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="4c204-526">Arabirimdeki üç özellik, başlatma ve <xref:System.Action> kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="4c204-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4c204-527">İptal Jetonu</span><span class="sxs-lookup"><span data-stu-id="4c204-527">Cancellation Token</span></span> | <span data-ttu-id="4c204-528">&#8230; tetiklenir</span><span class="sxs-lookup"><span data-stu-id="4c204-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="4c204-529">Ev sahibi tamamen başladı.</span><span class="sxs-lookup"><span data-stu-id="4c204-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="4c204-530">Ev sahibi zarif bir kapatma tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="4c204-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4c204-531">Tüm istekler işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-531">All requests should be processed.</span></span> <span data-ttu-id="4c204-532">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="4c204-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="4c204-533">Ev sahibi zarif bir kapatma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="4c204-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4c204-534">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-534">Requests may still be processing.</span></span> <span data-ttu-id="4c204-535">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="4c204-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="4c204-536">Yapıcı herhangi bir <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> sınıfa hizmet enjekte.</span><span class="sxs-lookup"><span data-stu-id="4c204-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="4c204-537">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) olayları kaydetmek için `LifetimeEventsHostedService` bir <xref:Microsoft.Extensions.Hosting.IHostedService> sınıf (bir uygulama) içine yapıcı enjeksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="4c204-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="4c204-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="4c204-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını talep ediyor.</span><span class="sxs-lookup"><span data-stu-id="4c204-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="4c204-540">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="4c204-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="4c204-541">Core şablonlarının ASP.NET .NET Core Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4c204-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4c204-542">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="4c204-542">Host definition</span></span>

<span data-ttu-id="4c204-543">*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:</span><span class="sxs-lookup"><span data-stu-id="4c204-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4c204-544">Bağımlılık enjeksiyonu (DI)</span><span class="sxs-lookup"><span data-stu-id="4c204-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="4c204-545">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="4c204-545">Logging</span></span>
* <span data-ttu-id="4c204-546">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4c204-546">Configuration</span></span>
* <span data-ttu-id="4c204-547">`IHostedService`Uygulama</span><span class="sxs-lookup"><span data-stu-id="4c204-547">`IHostedService` implementations</span></span>

<span data-ttu-id="4c204-548">Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="4c204-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="4c204-549">Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4c204-550">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="4c204-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4c204-551">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="4c204-551">Set up a host</span></span>

<span data-ttu-id="4c204-552">Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4c204-553">Yöntem: `Main`</span><span class="sxs-lookup"><span data-stu-id="4c204-553">The `Main` method:</span></span>

* <span data-ttu-id="4c204-554">Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="4c204-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4c204-555">Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.</span><span class="sxs-lookup"><span data-stu-id="4c204-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4c204-556">ASP.NET Core web şablonları bir ana bilgisayar oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4c204-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="4c204-557">Aşağıdaki kod, DI kapsayıcısına eklenen `IHostedService` bir uygulamayla HTTP dışı bir iş yükü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4c204-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="4c204-558">Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:</span><span class="sxs-lookup"><span data-stu-id="4c204-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4c204-559">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="4c204-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4c204-560">[Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4c204-561">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="4c204-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4c204-562">Varsayılan oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="4c204-562">Default builder settings</span></span>

<span data-ttu-id="4c204-563">Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="4c204-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4c204-564">İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4c204-565">Yükler ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="4c204-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="4c204-566">Çevre değişkenleri . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="4c204-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4c204-567">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-567">Command-line arguments.</span></span>
* <span data-ttu-id="4c204-568">Uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="4c204-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="4c204-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4c204-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="4c204-570">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="4c204-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4c204-571">[Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="4c204-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4c204-572">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-572">Environment variables.</span></span>
  * <span data-ttu-id="4c204-573">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4c204-573">Command-line arguments.</span></span>
* <span data-ttu-id="4c204-574">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="4c204-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4c204-575">Konsol</span><span class="sxs-lookup"><span data-stu-id="4c204-575">Console</span></span>
  * <span data-ttu-id="4c204-576">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="4c204-576">Debug</span></span>
  * <span data-ttu-id="4c204-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="4c204-577">EventSource</span></span>
  * <span data-ttu-id="4c204-578">EventLog (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="4c204-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4c204-579">Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)</span><span class="sxs-lookup"><span data-stu-id="4c204-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4c204-580">Yöntem: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="4c204-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4c204-581">`ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.</span><span class="sxs-lookup"><span data-stu-id="4c204-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4c204-582">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4c204-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4c204-583">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4c204-584">[Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4c204-585">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4c204-586">IIS tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-586">Enables IIS integration.</span></span> <span data-ttu-id="4c204-587">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4c204-588">Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4c204-589">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="4c204-589">Framework-provided services</span></span>

<span data-ttu-id="4c204-590">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="4c204-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4c204-591">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="4c204-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4c204-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4c204-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4c204-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4c204-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4c204-594">Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4c204-595">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="4c204-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="4c204-596">Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="4c204-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4c204-597">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="4c204-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4c204-598">Arabirim de `StopApplication` bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="4c204-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4c204-599">Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="4c204-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4c204-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4c204-600">IHostLifetime</span></span>

<span data-ttu-id="4c204-601">Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler.</span><span class="sxs-lookup"><span data-stu-id="4c204-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4c204-602">Son kayıtlı uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-602">The last implementation registered is used.</span></span>

<span data-ttu-id="4c204-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="4c204-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4c204-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4c204-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4c204-605"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="4c204-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4c204-606">[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="4c204-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4c204-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4c204-607">IHostEnvironment</span></span>

<span data-ttu-id="4c204-608">Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="4c204-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4c204-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4c204-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4c204-610">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="4c204-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4c204-611">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="4c204-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4c204-612">Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="4c204-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4c204-613">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4c204-613">Host configuration</span></span>

<span data-ttu-id="4c204-614">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4c204-615">Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4c204-616">Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4c204-617">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın.</span><span class="sxs-lookup"><span data-stu-id="4c204-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4c204-618">`ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4c204-619">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4c204-620">Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="4c204-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4c204-621">Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4c204-622">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4c204-623">Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="4c204-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4c204-624">Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4c204-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4c204-625">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4c204-625">App configuration</span></span>

<span data-ttu-id="4c204-626">Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4c204-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4c204-627">`ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4c204-628">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4c204-629">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4c204-630">Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4c204-631">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.</span><span class="sxs-lookup"><span data-stu-id="4c204-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4c204-632">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="4c204-632">Settings for all app types</span></span>

<span data-ttu-id="4c204-633">Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4c204-634">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4c204-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4c204-635">ApplicationName</span></span>

<span data-ttu-id="4c204-636">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4c204-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4c204-637">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4c204-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4c204-638">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-638">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-639">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="4c204-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4c204-640">**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4c204-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4c204-641">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c204-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4c204-642">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="4c204-642">ContentRoot</span></span>

<span data-ttu-id="4c204-643">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4c204-644">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="4c204-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4c204-645">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4c204-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4c204-646">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-646">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-647">**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="4c204-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4c204-648">**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4c204-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4c204-649">Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="4c204-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4c204-650">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-650">For more information, see:</span></span>

* [<span data-ttu-id="4c204-651">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="4c204-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4c204-652">Webroot</span><span class="sxs-lookup"><span data-stu-id="4c204-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4c204-653">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="4c204-653">EnvironmentName</span></span>

<span data-ttu-id="4c204-654">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4c204-655">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="4c204-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4c204-656">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="4c204-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4c204-657">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="4c204-657">**Key**: `environment`</span></span>  
<span data-ttu-id="4c204-658">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-658">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-659">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="4c204-659">**Default**: `Production`</span></span>  
<span data-ttu-id="4c204-660">**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4c204-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4c204-661">Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="4c204-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4c204-662">KapatmaTimeout</span><span class="sxs-lookup"><span data-stu-id="4c204-662">ShutdownTimeout</span></span>

<span data-ttu-id="4c204-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4c204-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4c204-664">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="4c204-664">The default value is five seconds.</span></span>  <span data-ttu-id="4c204-665">Zaman açılığı dönüşü nde, ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="4c204-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="4c204-666">Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4c204-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4c204-667">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.</span><span class="sxs-lookup"><span data-stu-id="4c204-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4c204-668">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="4c204-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4c204-669">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="4c204-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4c204-670">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="4c204-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4c204-671">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4c204-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4c204-672">**Türü**:`int`</span><span class="sxs-lookup"><span data-stu-id="4c204-672">**Type**: `int`</span></span>  
<span data-ttu-id="4c204-673">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="4c204-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4c204-674">**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4c204-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4c204-675">Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions`</span><span class="sxs-lookup"><span data-stu-id="4c204-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4c204-676">Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:</span><span class="sxs-lookup"><span data-stu-id="4c204-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="4c204-677">Değişiklikte uygulama yapılandırması yeniden yüklemeyi devre dışı kaldırma</span><span class="sxs-lookup"><span data-stu-id="4c204-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="4c204-678">[Varsayılan olarak,](xref:fundamentals/configuration/index#default) *appsettings.json* ve *appsettings.{ Dosya değiştiğinde Environment}.json* yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="4c204-679">Bu yeniden yükleme davranışını ASP.NET Core 5.0 Preview 3 `hostBuilder:reloadConfigOnChange` veya `false`sonraki ASP.NET devre dışı bırakabilirsiniz, anahtarı .</span><span class="sxs-lookup"><span data-stu-id="4c204-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="4c204-680">**Anahtar**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4c204-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4c204-681">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-682">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="4c204-682">**Default**: `true`</span></span>  
<span data-ttu-id="4c204-683">**Komut satırı bağımsız değişkeni**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4c204-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4c204-684">**Çevre değişkeni**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4c204-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="4c204-685">İki nokta`:`üst üste ( ) ayırıcı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="4c204-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="4c204-686">Daha fazla bilgi için [Bkz. Çevre değişkenleri.](xref:fundamentals/configuration/index#environment-variables)</span><span class="sxs-lookup"><span data-stu-id="4c204-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="4c204-687">Web uygulamaları için ayarlar</span><span class="sxs-lookup"><span data-stu-id="4c204-687">Settings for web apps</span></span>

<span data-ttu-id="4c204-688">Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4c204-689">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4c204-690">Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="4c204-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4c204-691">Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="4c204-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4c204-692">YakalamaBaşlangıç Hataları</span><span class="sxs-lookup"><span data-stu-id="4c204-692">CaptureStartupErrors</span></span>

<span data-ttu-id="4c204-693">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="4c204-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4c204-694">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="4c204-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4c204-695">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4c204-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4c204-696">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-697">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="4c204-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4c204-698">**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4c204-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4c204-699">Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4c204-700">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="4c204-700">DetailedErrors</span></span>

<span data-ttu-id="4c204-701">Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="4c204-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4c204-702">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4c204-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4c204-703">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-704">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="4c204-704">**Default**: `false`</span></span>  
<span data-ttu-id="4c204-705">**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4c204-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4c204-706">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4c204-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4c204-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="4c204-708">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="4c204-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4c204-709">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="4c204-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4c204-710">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4c204-711">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4c204-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4c204-712">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-712">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-713">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="4c204-713">**Default**: Empty string</span></span>  
<span data-ttu-id="4c204-714">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4c204-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4c204-715">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4c204-716">HostingStartupExcludeMontajies</span><span class="sxs-lookup"><span data-stu-id="4c204-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4c204-717">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="4c204-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4c204-718">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4c204-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4c204-719">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-719">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-720">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="4c204-720">**Default**: Empty string</span></span>  
<span data-ttu-id="4c204-721">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4c204-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4c204-722">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4c204-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4c204-723">HTTPS_Port</span></span>

<span data-ttu-id="4c204-724">HTTPS yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="4c204-724">The HTTPS redirect port.</span></span> <span data-ttu-id="4c204-725">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4c204-726">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="4c204-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="4c204-727">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-727">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-728">**Varsayılan**: Varsayılan değer ayarlı değil.</span><span class="sxs-lookup"><span data-stu-id="4c204-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4c204-729">**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4c204-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4c204-730">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c204-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4c204-731">TercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4c204-731">PreferHostingUrls</span></span>

<span data-ttu-id="4c204-732">Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4c204-733">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4c204-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4c204-734">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-735">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="4c204-735">**Default**: `true`</span></span>  
<span data-ttu-id="4c204-736">**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4c204-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4c204-737">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4c204-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4c204-738">HostingBaşlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="4c204-738">PreventHostingStartup</span></span>

<span data-ttu-id="4c204-739">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="4c204-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4c204-740">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4c204-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4c204-741">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4c204-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4c204-742">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="4c204-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4c204-743">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="4c204-743">**Default**: `false`</span></span>  
<span data-ttu-id="4c204-744">**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4c204-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4c204-745">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4c204-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4c204-746">Başlangıç Montaj</span><span class="sxs-lookup"><span data-stu-id="4c204-746">StartupAssembly</span></span>

<span data-ttu-id="4c204-747">`Startup` Sınıfı aramak için derleme.</span><span class="sxs-lookup"><span data-stu-id="4c204-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4c204-748">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4c204-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4c204-749">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-749">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-750">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="4c204-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4c204-751">**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4c204-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4c204-752">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="4c204-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4c204-753">`UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir).</span><span class="sxs-lookup"><span data-stu-id="4c204-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4c204-754">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4c204-755">URL’ler</span><span class="sxs-lookup"><span data-stu-id="4c204-755">URLs</span></span>

<span data-ttu-id="4c204-756">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi.</span><span class="sxs-lookup"><span data-stu-id="4c204-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4c204-757">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4c204-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4c204-758">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c204-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4c204-759">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="4c204-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4c204-760">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c204-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="4c204-761">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="4c204-761">**Key**: `urls`</span></span>  
<span data-ttu-id="4c204-762">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-762">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-763">**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4c204-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4c204-764">**Çevre değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4c204-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4c204-765">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="4c204-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4c204-766">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="4c204-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4c204-767">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4c204-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4c204-768">Webroot</span><span class="sxs-lookup"><span data-stu-id="4c204-768">WebRoot</span></span>

<span data-ttu-id="4c204-769">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği uygulamanın statik varlıklarıiçin göreli yolu belirler.</span><span class="sxs-lookup"><span data-stu-id="4c204-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4c204-770">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c204-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4c204-771">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="4c204-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="4c204-772">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="4c204-772">**Type**: `string`</span></span>  
<span data-ttu-id="4c204-773">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="4c204-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4c204-774">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4c204-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4c204-775">**Çevre değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4c204-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4c204-776">Bu değeri ayarlamak için, ortam `UseWebRoot` değişkenini kullanın veya çağırın: `IWebHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="4c204-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4c204-777">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4c204-777">For more information, see:</span></span>

* [<span data-ttu-id="4c204-778">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="4c204-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4c204-779">İçerikKök</span><span class="sxs-lookup"><span data-stu-id="4c204-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4c204-780">Ev sahibinin ömrünü yönetin</span><span class="sxs-lookup"><span data-stu-id="4c204-780">Manage the host lifetime</span></span>

<span data-ttu-id="4c204-781">Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın.</span><span class="sxs-lookup"><span data-stu-id="4c204-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4c204-782">Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="4c204-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4c204-783">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="4c204-783">Run</span></span>

<span data-ttu-id="4c204-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="4c204-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4c204-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-785">RunAsync</span></span>

<span data-ttu-id="4c204-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.</span><span class="sxs-lookup"><span data-stu-id="4c204-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4c204-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-787">RunConsoleAsync</span></span>

<span data-ttu-id="4c204-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="4c204-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4c204-789">Başlat</span><span class="sxs-lookup"><span data-stu-id="4c204-789">Start</span></span>

<span data-ttu-id="4c204-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="4c204-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4c204-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-791">StartAsync</span></span>

<span data-ttu-id="4c204-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir.</span><span class="sxs-lookup"><span data-stu-id="4c204-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4c204-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="4c204-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4c204-794">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4c204-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4c204-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-795">StopAsync</span></span>

<span data-ttu-id="4c204-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4c204-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4c204-797">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="4c204-797">WaitForShutdown</span></span>

<span data-ttu-id="4c204-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.</span><span class="sxs-lookup"><span data-stu-id="4c204-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4c204-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4c204-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="4c204-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="4c204-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4c204-801">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="4c204-801">External control</span></span>

<span data-ttu-id="4c204-802">Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="4c204-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4c204-803">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4c204-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
