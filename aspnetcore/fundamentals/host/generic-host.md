---
title: .NET Genel Ana Bilgisayar
author: rick-anderson
description: Uygulama başlatma ve yaşam boyu yönetimden sorumlu .NET Core Generic Host hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 454216cec72048217ede412f8ff6d4261f7353b1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417628"
---
# <a name="net-generic-host"></a><span data-ttu-id="5197e-103">.NET Genel Ana Bilgisayar</span><span class="sxs-lookup"><span data-stu-id="5197e-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5197e-104">Bu makalede, .NET Çekirdek Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) tanıtır ve nasıl kullanılacağı hakkında rehberlik sağlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="5197e-105">Ev sahibi nedir?</span><span class="sxs-lookup"><span data-stu-id="5197e-105">What's a host?</span></span>

<span data-ttu-id="5197e-106">*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:</span><span class="sxs-lookup"><span data-stu-id="5197e-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5197e-107">Bağımlılık enjeksiyonu (DI)</span><span class="sxs-lookup"><span data-stu-id="5197e-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="5197e-108">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="5197e-108">Logging</span></span>
* <span data-ttu-id="5197e-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5197e-109">Configuration</span></span>
* <span data-ttu-id="5197e-110">`IHostedService`Uygulama</span><span class="sxs-lookup"><span data-stu-id="5197e-110">`IHostedService` implementations</span></span>

<span data-ttu-id="5197e-111">Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="5197e-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="5197e-112">Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5197e-113">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="5197e-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="5197e-114">3.0'dan önce ASP.NET Core [sürümlerinde, Web Host](xref:fundamentals/host/web-host) HTTP iş yükleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="5197e-115">Web Barındırma artık web uygulamaları için tavsiye edilmez ve yalnızca geriye dönük uyumluluk için kullanılabilir durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="5197e-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5197e-116">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="5197e-116">Set up a host</span></span>

<span data-ttu-id="5197e-117">Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5197e-118">Yöntem: `Main`</span><span class="sxs-lookup"><span data-stu-id="5197e-118">The `Main` method:</span></span>

* <span data-ttu-id="5197e-119">Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="5197e-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5197e-120">Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.</span><span class="sxs-lookup"><span data-stu-id="5197e-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5197e-121">Burada, *Program.cs* HTTP olmayan bir iş yükü için `IHostedService` Program.cs kodu ve DI kapsayıcısına tek bir uygulama eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="5197e-122">Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:</span><span class="sxs-lookup"><span data-stu-id="5197e-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5197e-123">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="5197e-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5197e-124">[Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5197e-125">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="5197e-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5197e-126">Varsayılan oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="5197e-126">Default builder settings</span></span>

<span data-ttu-id="5197e-127">Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="5197e-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="5197e-128">İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="5197e-129">Yükler ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5197e-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="5197e-130">Çevre değişkenleri . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="5197e-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5197e-131">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-131">Command-line arguments.</span></span>
* <span data-ttu-id="5197e-132">Uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5197e-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="5197e-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5197e-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="5197e-134">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="5197e-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5197e-135">[Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="5197e-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5197e-136">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-136">Environment variables.</span></span>
  * <span data-ttu-id="5197e-137">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-137">Command-line arguments.</span></span>
* <span data-ttu-id="5197e-138">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="5197e-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5197e-139">Konsol</span><span class="sxs-lookup"><span data-stu-id="5197e-139">Console</span></span>
  * <span data-ttu-id="5197e-140">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="5197e-140">Debug</span></span>
  * <span data-ttu-id="5197e-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="5197e-141">EventSource</span></span>
  * <span data-ttu-id="5197e-142">EventLog (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="5197e-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5197e-143">Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)</span><span class="sxs-lookup"><span data-stu-id="5197e-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5197e-144">Yöntem: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="5197e-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5197e-145">`ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.</span><span class="sxs-lookup"><span data-stu-id="5197e-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5197e-146">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5197e-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5197e-147">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5197e-148">[Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5197e-149">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5197e-150">IIS tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-150">Enables IIS integration.</span></span> <span data-ttu-id="5197e-151">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5197e-152">Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5197e-153">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="5197e-153">Framework-provided services</span></span>

<span data-ttu-id="5197e-154">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5197e-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5197e-155">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5197e-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5197e-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5197e-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5197e-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5197e-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5197e-158">Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5197e-159">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5197e-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="5197e-160">Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="5197e-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5197e-161">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="5197e-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5197e-162">Arabirim de `StopApplication` bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="5197e-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5197e-163">Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="5197e-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5197e-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5197e-164">IHostLifetime</span></span>

<span data-ttu-id="5197e-165">Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler.</span><span class="sxs-lookup"><span data-stu-id="5197e-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5197e-166">Son kayıtlı uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-166">The last implementation registered is used.</span></span>

<span data-ttu-id="5197e-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="5197e-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5197e-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5197e-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5197e-169"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="5197e-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="5197e-170">[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="5197e-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5197e-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5197e-171">IHostEnvironment</span></span>

<span data-ttu-id="5197e-172">Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="5197e-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5197e-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5197e-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5197e-174">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5197e-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5197e-175">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5197e-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="5197e-176">Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="5197e-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5197e-177">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5197e-177">Host configuration</span></span>

<span data-ttu-id="5197e-178">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5197e-179">Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="5197e-180">Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5197e-181">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın.</span><span class="sxs-lookup"><span data-stu-id="5197e-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5197e-182">`ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5197e-183">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5197e-184">Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="5197e-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5197e-185">Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5197e-186">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5197e-187">Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="5197e-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5197e-188">Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5197e-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5197e-189">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5197e-189">App configuration</span></span>

<span data-ttu-id="5197e-190">Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5197e-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5197e-191">`ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5197e-192">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5197e-193">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5197e-194">Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5197e-195">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.</span><span class="sxs-lookup"><span data-stu-id="5197e-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5197e-196">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5197e-196">Settings for all app types</span></span>

<span data-ttu-id="5197e-197">Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5197e-198">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5197e-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5197e-199">ApplicationName</span></span>

<span data-ttu-id="5197e-200">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5197e-201">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="5197e-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5197e-202">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-202">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-203">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="5197e-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5197e-204">**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5197e-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5197e-205">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5197e-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="5197e-206">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5197e-206">ContentRootPath</span></span>

<span data-ttu-id="5197e-207">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="5197e-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5197e-208">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="5197e-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5197e-209">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5197e-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5197e-210">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-210">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-211">**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="5197e-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5197e-212">**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5197e-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5197e-213">Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5197e-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5197e-214">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-214">For more information, see:</span></span>

* [<span data-ttu-id="5197e-215">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="5197e-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5197e-216">Webroot</span><span class="sxs-lookup"><span data-stu-id="5197e-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5197e-217">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5197e-217">EnvironmentName</span></span>

<span data-ttu-id="5197e-218">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5197e-219">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="5197e-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5197e-220">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5197e-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5197e-221">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="5197e-221">**Key**: `environment`</span></span>  
<span data-ttu-id="5197e-222">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-222">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-223">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="5197e-223">**Default**: `Production`</span></span>  
<span data-ttu-id="5197e-224">**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5197e-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5197e-225">Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5197e-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5197e-226">KapatmaTimeout</span><span class="sxs-lookup"><span data-stu-id="5197e-226">ShutdownTimeout</span></span>

<span data-ttu-id="5197e-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5197e-228">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5197e-228">The default value is five seconds.</span></span>  <span data-ttu-id="5197e-229">Zaman açılığı dönüşü nde, ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="5197e-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="5197e-230">Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5197e-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5197e-231">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.</span><span class="sxs-lookup"><span data-stu-id="5197e-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5197e-232">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="5197e-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5197e-233">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="5197e-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5197e-234">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="5197e-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5197e-235">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5197e-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5197e-236">**Türü**:`int`</span><span class="sxs-lookup"><span data-stu-id="5197e-236">**Type**: `int`</span></span>  
<span data-ttu-id="5197e-237">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="5197e-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5197e-238">**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5197e-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5197e-239">Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions`</span><span class="sxs-lookup"><span data-stu-id="5197e-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5197e-240">Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5197e-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="5197e-241">Değişiklikte uygulama yapılandırması yeniden yüklemeyi devre dışı kaldırma</span><span class="sxs-lookup"><span data-stu-id="5197e-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="5197e-242">[Varsayılan olarak,](xref:fundamentals/configuration/index#default) *appsettings.json* ve *appsettings.{ Dosya değiştiğinde Environment}.json* yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="5197e-243">Bu yeniden yükleme davranışını ASP.NET Core 5.0 Preview 3 `hostBuilder:reloadConfigOnChange` veya `false`sonraki ASP.NET devre dışı bırakabilirsiniz, anahtarı .</span><span class="sxs-lookup"><span data-stu-id="5197e-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="5197e-244">**Anahtar**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5197e-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5197e-245">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-246">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="5197e-246">**Default**: `true`</span></span>  
<span data-ttu-id="5197e-247">**Komut satırı bağımsız değişkeni**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5197e-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5197e-248">**Çevre değişkeni**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5197e-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="5197e-249">İki nokta`:`üst üste ( ) ayırıcı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="5197e-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="5197e-250">Daha fazla bilgi için [Bkz. Çevre değişkenleri.](xref:fundamentals/configuration/index#environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5197e-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="5197e-251">Web uygulamaları için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5197e-251">Settings for web apps</span></span>

<span data-ttu-id="5197e-252">Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5197e-253">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5197e-254">Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="5197e-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5197e-255">Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="5197e-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5197e-256">YakalamaBaşlangıç Hataları</span><span class="sxs-lookup"><span data-stu-id="5197e-256">CaptureStartupErrors</span></span>

<span data-ttu-id="5197e-257">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="5197e-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5197e-258">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="5197e-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5197e-259">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5197e-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5197e-260">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-261">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="5197e-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5197e-262">**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5197e-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5197e-263">Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5197e-264">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="5197e-264">DetailedErrors</span></span>

<span data-ttu-id="5197e-265">Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="5197e-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5197e-266">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5197e-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5197e-267">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-268">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5197e-268">**Default**: `false`</span></span>  
<span data-ttu-id="5197e-269">**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5197e-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5197e-270">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5197e-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5197e-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="5197e-272">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5197e-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5197e-273">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="5197e-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5197e-274">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="5197e-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5197e-275">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5197e-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5197e-276">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-276">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-277">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5197e-277">**Default**: Empty string</span></span>  
<span data-ttu-id="5197e-278">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5197e-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5197e-279">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5197e-280">HostingStartupExcludeMontajies</span><span class="sxs-lookup"><span data-stu-id="5197e-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5197e-281">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5197e-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5197e-282">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5197e-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5197e-283">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-283">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-284">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5197e-284">**Default**: Empty string</span></span>  
<span data-ttu-id="5197e-285">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5197e-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5197e-286">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5197e-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5197e-287">HTTPS_Port</span></span>

<span data-ttu-id="5197e-288">HTTPS yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="5197e-288">The HTTPS redirect port.</span></span> <span data-ttu-id="5197e-289">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5197e-290">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="5197e-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="5197e-291">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-291">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-292">**Varsayılan**: Varsayılan değer ayarlı değil.</span><span class="sxs-lookup"><span data-stu-id="5197e-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5197e-293">**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5197e-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5197e-294">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5197e-295">TercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5197e-295">PreferHostingUrls</span></span>

<span data-ttu-id="5197e-296">Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5197e-297">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5197e-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5197e-298">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-299">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="5197e-299">**Default**: `true`</span></span>  
<span data-ttu-id="5197e-300">**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5197e-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5197e-301">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5197e-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5197e-302">HostingBaşlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="5197e-302">PreventHostingStartup</span></span>

<span data-ttu-id="5197e-303">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5197e-304">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5197e-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5197e-305">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5197e-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5197e-306">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-307">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5197e-307">**Default**: `false`</span></span>  
<span data-ttu-id="5197e-308">**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5197e-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5197e-309">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5197e-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5197e-310">Başlangıç Montaj</span><span class="sxs-lookup"><span data-stu-id="5197e-310">StartupAssembly</span></span>

<span data-ttu-id="5197e-311">`Startup` Sınıfı aramak için derleme.</span><span class="sxs-lookup"><span data-stu-id="5197e-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5197e-312">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5197e-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5197e-313">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-313">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-314">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="5197e-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5197e-315">**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5197e-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5197e-316">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="5197e-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5197e-317">`UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir).</span><span class="sxs-lookup"><span data-stu-id="5197e-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5197e-318">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5197e-319">URL’ler</span><span class="sxs-lookup"><span data-stu-id="5197e-319">URLs</span></span>

<span data-ttu-id="5197e-320">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi.</span><span class="sxs-lookup"><span data-stu-id="5197e-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5197e-321">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5197e-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5197e-322">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="5197e-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5197e-323">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5197e-324">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="5197e-325">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="5197e-325">**Key**: `urls`</span></span>  
<span data-ttu-id="5197e-326">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-326">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-327">**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5197e-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5197e-328">**Çevre değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5197e-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5197e-329">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="5197e-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5197e-330">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="5197e-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5197e-331">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5197e-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5197e-332">Webroot</span><span class="sxs-lookup"><span data-stu-id="5197e-332">WebRoot</span></span>

<span data-ttu-id="5197e-333">Uygulamanın statik varlıklarına göreli yol.</span><span class="sxs-lookup"><span data-stu-id="5197e-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="5197e-334">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="5197e-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="5197e-335">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-335">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-336">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="5197e-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5197e-337">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5197e-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="5197e-338">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="5197e-339">**Çevre değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5197e-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5197e-340">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="5197e-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5197e-341">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-341">For more information, see:</span></span>

* [<span data-ttu-id="5197e-342">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="5197e-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5197e-343">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5197e-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5197e-344">Ev sahibinin ömrünü yönetin</span><span class="sxs-lookup"><span data-stu-id="5197e-344">Manage the host lifetime</span></span>

<span data-ttu-id="5197e-345">Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın.</span><span class="sxs-lookup"><span data-stu-id="5197e-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5197e-346">Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="5197e-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5197e-347">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5197e-347">Run</span></span>

<span data-ttu-id="5197e-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5197e-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5197e-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-349">RunAsync</span></span>

<span data-ttu-id="5197e-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.</span><span class="sxs-lookup"><span data-stu-id="5197e-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5197e-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-351">RunConsoleAsync</span></span>

<span data-ttu-id="5197e-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5197e-353">Başlat</span><span class="sxs-lookup"><span data-stu-id="5197e-353">Start</span></span>

<span data-ttu-id="5197e-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="5197e-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5197e-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-355">StartAsync</span></span>

<span data-ttu-id="5197e-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir.</span><span class="sxs-lookup"><span data-stu-id="5197e-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5197e-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="5197e-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5197e-358">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5197e-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-359">StopAsync</span></span>

<span data-ttu-id="5197e-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5197e-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5197e-361">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="5197e-361">WaitForShutdown</span></span>

<span data-ttu-id="5197e-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.</span><span class="sxs-lookup"><span data-stu-id="5197e-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5197e-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="5197e-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="5197e-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5197e-365">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="5197e-365">External control</span></span>

<span data-ttu-id="5197e-366">Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="5197e-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="5197e-367">Bu makalede, .NET Çekirdek Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) tanıtır ve nasıl kullanılacağı hakkında rehberlik sağlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="5197e-368">Ev sahibi nedir?</span><span class="sxs-lookup"><span data-stu-id="5197e-368">What's a host?</span></span>

<span data-ttu-id="5197e-369">*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:</span><span class="sxs-lookup"><span data-stu-id="5197e-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5197e-370">Bağımlılık enjeksiyonu (DI)</span><span class="sxs-lookup"><span data-stu-id="5197e-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="5197e-371">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="5197e-371">Logging</span></span>
* <span data-ttu-id="5197e-372">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5197e-372">Configuration</span></span>
* <span data-ttu-id="5197e-373">`IHostedService`Uygulama</span><span class="sxs-lookup"><span data-stu-id="5197e-373">`IHostedService` implementations</span></span>

<span data-ttu-id="5197e-374">Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="5197e-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="5197e-375">Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5197e-376">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="5197e-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="5197e-377">3.0'dan önce ASP.NET Core [sürümlerinde, Web Host](xref:fundamentals/host/web-host) HTTP iş yükleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="5197e-378">Web Barındırma artık web uygulamaları için tavsiye edilmez ve yalnızca geriye dönük uyumluluk için kullanılabilir durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="5197e-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5197e-379">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="5197e-379">Set up a host</span></span>

<span data-ttu-id="5197e-380">Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5197e-381">Yöntem: `Main`</span><span class="sxs-lookup"><span data-stu-id="5197e-381">The `Main` method:</span></span>

* <span data-ttu-id="5197e-382">Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="5197e-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5197e-383">Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.</span><span class="sxs-lookup"><span data-stu-id="5197e-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5197e-384">Burada, *Program.cs* HTTP olmayan bir iş yükü için `IHostedService` Program.cs kodu ve DI kapsayıcısına tek bir uygulama eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="5197e-385">Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:</span><span class="sxs-lookup"><span data-stu-id="5197e-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5197e-386">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="5197e-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5197e-387">[Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5197e-388">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="5197e-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5197e-389">Varsayılan oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="5197e-389">Default builder settings</span></span>

<span data-ttu-id="5197e-390">Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="5197e-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="5197e-391">İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="5197e-392">Yükler ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5197e-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="5197e-393">Çevre değişkenleri . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="5197e-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5197e-394">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-394">Command-line arguments.</span></span>
* <span data-ttu-id="5197e-395">Uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5197e-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="5197e-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5197e-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="5197e-397">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="5197e-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5197e-398">[Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="5197e-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5197e-399">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-399">Environment variables.</span></span>
  * <span data-ttu-id="5197e-400">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-400">Command-line arguments.</span></span>
* <span data-ttu-id="5197e-401">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="5197e-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5197e-402">Konsol</span><span class="sxs-lookup"><span data-stu-id="5197e-402">Console</span></span>
  * <span data-ttu-id="5197e-403">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="5197e-403">Debug</span></span>
  * <span data-ttu-id="5197e-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="5197e-404">EventSource</span></span>
  * <span data-ttu-id="5197e-405">EventLog (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="5197e-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5197e-406">Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)</span><span class="sxs-lookup"><span data-stu-id="5197e-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5197e-407">Yöntem: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="5197e-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5197e-408">`ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.</span><span class="sxs-lookup"><span data-stu-id="5197e-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5197e-409">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5197e-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5197e-410">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5197e-411">[Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5197e-412">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5197e-413">IIS tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-413">Enables IIS integration.</span></span> <span data-ttu-id="5197e-414">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5197e-415">Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5197e-416">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="5197e-416">Framework-provided services</span></span>

<span data-ttu-id="5197e-417">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5197e-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5197e-418">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5197e-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5197e-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5197e-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5197e-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5197e-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5197e-421">Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5197e-422">IHostApplicationÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5197e-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="5197e-423">Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="5197e-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5197e-424">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="5197e-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5197e-425">Arabirim de `StopApplication` bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="5197e-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5197e-426">Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="5197e-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5197e-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5197e-427">IHostLifetime</span></span>

<span data-ttu-id="5197e-428">Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler.</span><span class="sxs-lookup"><span data-stu-id="5197e-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5197e-429">Son kayıtlı uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-429">The last implementation registered is used.</span></span>

<span data-ttu-id="5197e-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="5197e-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5197e-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5197e-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5197e-432"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="5197e-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="5197e-433">[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="5197e-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5197e-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5197e-434">IHostEnvironment</span></span>

<span data-ttu-id="5197e-435">Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="5197e-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5197e-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5197e-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5197e-437">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5197e-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5197e-438">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5197e-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="5197e-439">Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="5197e-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5197e-440">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5197e-440">Host configuration</span></span>

<span data-ttu-id="5197e-441">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5197e-442">Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="5197e-443">Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5197e-444">Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın.</span><span class="sxs-lookup"><span data-stu-id="5197e-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5197e-445">`ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5197e-446">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5197e-447">Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="5197e-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5197e-448">Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5197e-449">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5197e-450">Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="5197e-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5197e-451">Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5197e-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5197e-452">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5197e-452">App configuration</span></span>

<span data-ttu-id="5197e-453">Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5197e-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5197e-454">`ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5197e-455">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5197e-456">Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5197e-457">Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5197e-458">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.</span><span class="sxs-lookup"><span data-stu-id="5197e-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5197e-459">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5197e-459">Settings for all app types</span></span>

<span data-ttu-id="5197e-460">Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5197e-461">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5197e-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5197e-462">ApplicationName</span></span>

<span data-ttu-id="5197e-463">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5197e-464">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="5197e-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5197e-465">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-465">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-466">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="5197e-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5197e-467">**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5197e-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5197e-468">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5197e-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="5197e-469">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5197e-469">ContentRootPath</span></span>

<span data-ttu-id="5197e-470">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="5197e-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5197e-471">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="5197e-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5197e-472">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5197e-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5197e-473">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-473">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-474">**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="5197e-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5197e-475">**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5197e-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5197e-476">Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5197e-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5197e-477">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-477">For more information, see:</span></span>

* [<span data-ttu-id="5197e-478">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="5197e-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5197e-479">Webroot</span><span class="sxs-lookup"><span data-stu-id="5197e-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5197e-480">Çevre Adı</span><span class="sxs-lookup"><span data-stu-id="5197e-480">EnvironmentName</span></span>

<span data-ttu-id="5197e-481">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5197e-482">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="5197e-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5197e-483">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5197e-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5197e-484">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="5197e-484">**Key**: `environment`</span></span>  
<span data-ttu-id="5197e-485">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-485">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-486">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="5197e-486">**Default**: `Production`</span></span>  
<span data-ttu-id="5197e-487">**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5197e-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5197e-488">Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="5197e-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5197e-489">KapatmaTimeout</span><span class="sxs-lookup"><span data-stu-id="5197e-489">ShutdownTimeout</span></span>

<span data-ttu-id="5197e-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5197e-491">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5197e-491">The default value is five seconds.</span></span>  <span data-ttu-id="5197e-492">Zaman açılığı dönüşü nde, ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="5197e-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="5197e-493">Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5197e-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5197e-494">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.</span><span class="sxs-lookup"><span data-stu-id="5197e-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5197e-495">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="5197e-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5197e-496">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="5197e-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5197e-497">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="5197e-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5197e-498">**Anahtar**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5197e-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5197e-499">**Türü**:`int`</span><span class="sxs-lookup"><span data-stu-id="5197e-499">**Type**: `int`</span></span>  
<span data-ttu-id="5197e-500">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="5197e-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5197e-501">**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5197e-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5197e-502">Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions`</span><span class="sxs-lookup"><span data-stu-id="5197e-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5197e-503">Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5197e-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="5197e-504">Web uygulamaları için ayarlar</span><span class="sxs-lookup"><span data-stu-id="5197e-504">Settings for web apps</span></span>

<span data-ttu-id="5197e-505">Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5197e-506">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5197e-507">Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="5197e-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5197e-508">Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="5197e-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5197e-509">YakalamaBaşlangıç Hataları</span><span class="sxs-lookup"><span data-stu-id="5197e-509">CaptureStartupErrors</span></span>

<span data-ttu-id="5197e-510">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="5197e-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5197e-511">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="5197e-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5197e-512">**Anahtar**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5197e-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5197e-513">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-514">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="5197e-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5197e-515">**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5197e-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5197e-516">Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5197e-517">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="5197e-517">DetailedErrors</span></span>

<span data-ttu-id="5197e-518">Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="5197e-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5197e-519">**Anahtar**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5197e-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5197e-520">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-521">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5197e-521">**Default**: `false`</span></span>  
<span data-ttu-id="5197e-522">**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5197e-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5197e-523">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5197e-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5197e-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="5197e-525">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5197e-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5197e-526">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="5197e-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5197e-527">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="5197e-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5197e-528">**Anahtar**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5197e-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5197e-529">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-529">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-530">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5197e-530">**Default**: Empty string</span></span>  
<span data-ttu-id="5197e-531">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5197e-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5197e-532">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5197e-533">HostingStartupExcludeMontajies</span><span class="sxs-lookup"><span data-stu-id="5197e-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5197e-534">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="5197e-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5197e-535">**Anahtar**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5197e-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5197e-536">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-536">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-537">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="5197e-537">**Default**: Empty string</span></span>  
<span data-ttu-id="5197e-538">**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5197e-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5197e-539">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5197e-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5197e-540">HTTPS_Port</span></span>

<span data-ttu-id="5197e-541">HTTPS yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="5197e-541">The HTTPS redirect port.</span></span> <span data-ttu-id="5197e-542">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5197e-543">**Anahtar**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="5197e-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="5197e-544">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-544">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-545">**Varsayılan**: Varsayılan değer ayarlı değil.</span><span class="sxs-lookup"><span data-stu-id="5197e-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5197e-546">**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5197e-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5197e-547">Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5197e-548">TercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5197e-548">PreferHostingUrls</span></span>

<span data-ttu-id="5197e-549">Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5197e-550">**Anahtar**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5197e-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5197e-551">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-552">**Varsayılan**:`true`</span><span class="sxs-lookup"><span data-stu-id="5197e-552">**Default**: `true`</span></span>  
<span data-ttu-id="5197e-553">**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5197e-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5197e-554">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5197e-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5197e-555">HostingBaşlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="5197e-555">PreventHostingStartup</span></span>

<span data-ttu-id="5197e-556">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5197e-557">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5197e-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5197e-558">**Anahtar**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5197e-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5197e-559">**Türü** `bool` :`true` `1`( veya )</span><span class="sxs-lookup"><span data-stu-id="5197e-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5197e-560">**Varsayılan**:`false`</span><span class="sxs-lookup"><span data-stu-id="5197e-560">**Default**: `false`</span></span>  
<span data-ttu-id="5197e-561">**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5197e-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5197e-562">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5197e-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5197e-563">Başlangıç Montaj</span><span class="sxs-lookup"><span data-stu-id="5197e-563">StartupAssembly</span></span>

<span data-ttu-id="5197e-564">`Startup` Sınıfı aramak için derleme.</span><span class="sxs-lookup"><span data-stu-id="5197e-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5197e-565">**Anahtar**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5197e-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5197e-566">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-566">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-567">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="5197e-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5197e-568">**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5197e-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5197e-569">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="5197e-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5197e-570">`UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir).</span><span class="sxs-lookup"><span data-stu-id="5197e-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5197e-571">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5197e-572">URL’ler</span><span class="sxs-lookup"><span data-stu-id="5197e-572">URLs</span></span>

<span data-ttu-id="5197e-573">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi.</span><span class="sxs-lookup"><span data-stu-id="5197e-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5197e-574">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5197e-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5197e-575">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="5197e-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5197e-576">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5197e-577">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="5197e-578">**Anahtar**:`urls`</span><span class="sxs-lookup"><span data-stu-id="5197e-578">**Key**: `urls`</span></span>  
<span data-ttu-id="5197e-579">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-579">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-580">**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5197e-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5197e-581">**Çevre değişkeni**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5197e-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5197e-582">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="5197e-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5197e-583">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="5197e-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5197e-584">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5197e-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5197e-585">Webroot</span><span class="sxs-lookup"><span data-stu-id="5197e-585">WebRoot</span></span>

<span data-ttu-id="5197e-586">Uygulamanın statik varlıklarına göreli yol.</span><span class="sxs-lookup"><span data-stu-id="5197e-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="5197e-587">**Anahtar**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="5197e-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="5197e-588">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-588">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-589">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="5197e-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5197e-590">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5197e-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="5197e-591">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="5197e-592">**Çevre değişkeni**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5197e-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5197e-593">Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="5197e-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5197e-594">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5197e-594">For more information, see:</span></span>

* [<span data-ttu-id="5197e-595">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="5197e-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5197e-596">İçerikRootPath</span><span class="sxs-lookup"><span data-stu-id="5197e-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5197e-597">Ev sahibinin ömrünü yönetin</span><span class="sxs-lookup"><span data-stu-id="5197e-597">Manage the host lifetime</span></span>

<span data-ttu-id="5197e-598">Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın.</span><span class="sxs-lookup"><span data-stu-id="5197e-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5197e-599">Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="5197e-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5197e-600">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5197e-600">Run</span></span>

<span data-ttu-id="5197e-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5197e-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5197e-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-602">RunAsync</span></span>

<span data-ttu-id="5197e-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.</span><span class="sxs-lookup"><span data-stu-id="5197e-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5197e-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-604">RunConsoleAsync</span></span>

<span data-ttu-id="5197e-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5197e-606">Başlat</span><span class="sxs-lookup"><span data-stu-id="5197e-606">Start</span></span>

<span data-ttu-id="5197e-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="5197e-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5197e-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-608">StartAsync</span></span>

<span data-ttu-id="5197e-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir.</span><span class="sxs-lookup"><span data-stu-id="5197e-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5197e-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="5197e-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5197e-611">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5197e-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-612">StopAsync</span></span>

<span data-ttu-id="5197e-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5197e-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5197e-614">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="5197e-614">WaitForShutdown</span></span>

<span data-ttu-id="5197e-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.</span><span class="sxs-lookup"><span data-stu-id="5197e-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5197e-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="5197e-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="5197e-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5197e-618">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="5197e-618">External control</span></span>

<span data-ttu-id="5197e-619">Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="5197e-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5197e-620">ASP.NET Core uygulamaları bir ana bilgisayar yapılandırır ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="5197e-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="5197e-621">Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="5197e-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="5197e-622">Bu makale, HTTP isteklerini<xref:Microsoft.Extensions.Hosting.HostBuilder>işlemeyan uygulamalar için kullanılan ASP.NET Core Genel Ana Bilgisayar (),</span><span class="sxs-lookup"><span data-stu-id="5197e-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="5197e-623">Genel Ana Bilgisayar'ın amacı, daha geniş bir ana bilgisayar senaryosu dizisi sağlamak için HTTP ardışık hattını Web Ana Bilgisayar API'sinden ayırmaktır.</span><span class="sxs-lookup"><span data-stu-id="5197e-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="5197e-624">Genel Ana Bilgisayar'a dayalı mesajlaşma, arka plan görevleri ve diğer HTTP dışındaki iş yükleri yapılandırma, bağımlılık enjeksiyonu (DI) ve günlüğe kaydetme gibi çapraz kesme özelliklerinden yararlanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="5197e-625">Genel Ana Bilgisayar, ASP.NET Core 2.1'de yenidir ve web barındırma senaryoları için uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="5197e-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="5197e-626">Web barındırma senaryoları için [Web Barındıran Alanı'nı](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="5197e-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5197e-627">Genel Ana Bilgisayar, gelecekteki bir sürümde Web Host'un yerini alır ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API'si olarak hareket eder.</span><span class="sxs-lookup"><span data-stu-id="5197e-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="5197e-628">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5197e-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5197e-629">Örnek uygulamayı Visual [Studio Code'da](https://code.visualstudio.com/)çalıştırırken *harici veya entegre*bir terminal kullanın.</span><span class="sxs-lookup"><span data-stu-id="5197e-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="5197e-630">Örneği bir `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="5197e-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="5197e-631">Konsolu Visual Studio Code'da ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="5197e-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="5197e-632">*.vscode/launch.json* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="5197e-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="5197e-633">**.NET Core Launch (konsol)** yapılandırmasında, **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="5197e-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="5197e-634">Değeri ya da `externalTerminal` `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="5197e-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="5197e-635">Giriş</span><span class="sxs-lookup"><span data-stu-id="5197e-635">Introduction</span></span>

<span data-ttu-id="5197e-636">Genel Ana Bilgisayar kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="5197e-637">Paket `Microsoft.Extensions.Hosting` [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (core 2.1 veya daha sonra ASP.NET) dahildir.</span><span class="sxs-lookup"><span data-stu-id="5197e-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="5197e-638"><xref:Microsoft.Extensions.Hosting.IHostedService>kod yürütmeiçin giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="5197e-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="5197e-639">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama [ConfigureServices hizmet kaydı](#configureservices)sırasına göre yürütülür.</span><span class="sxs-lookup"><span data-stu-id="5197e-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="5197e-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>ana bilgisayar <xref:Microsoft.Extensions.Hosting.IHostedService> başladığında her birine <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> çağrılır ve ana bilgisayar zarif bir şekilde kapandığında ters kayıt sırasına çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5197e-641">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="5197e-641">Set up a host</span></span>

<span data-ttu-id="5197e-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder>kitaplıkların ve uygulamaların ana bilgisayarı başlatma, oluşturmak ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="5197e-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="5197e-643">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="5197e-643">Options</span></span>

<span data-ttu-id="5197e-644"><xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırmak <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="5197e-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="5197e-645">Kapatma zaman</span><span class="sxs-lookup"><span data-stu-id="5197e-645">Shutdown timeout</span></span>

<span data-ttu-id="5197e-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5197e-647">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5197e-647">The default value is five seconds.</span></span>

<span data-ttu-id="5197e-648">Aşağıdaki seçenek yapılandırmasında `Program.Main` varsayılan beş saniyelik kapatma zaman dışarısını 20 saniyeye çıkarır:</span><span class="sxs-lookup"><span data-stu-id="5197e-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="5197e-649">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="5197e-649">Default services</span></span>

<span data-ttu-id="5197e-650">Aşağıdaki hizmetler ana bilgisayar başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5197e-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="5197e-651">[Çevre](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="5197e-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="5197e-652">[Yapılandırma](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="5197e-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="5197e-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5197e-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="5197e-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5197e-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="5197e-655">[Seçenekler](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="5197e-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="5197e-656">[Günlük](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="5197e-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5197e-657">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5197e-657">Host configuration</span></span>

<span data-ttu-id="5197e-658">Ana bilgisayar yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="5197e-658">Host configuration is created by:</span></span>

* <span data-ttu-id="5197e-659">İçerik <xref:Microsoft.Extensions.Hosting.IHostBuilder> [kökünü](#content-root) ve [ortamını](#environment)ayarlamak için uzantı yöntemlerini çağırma.</span><span class="sxs-lookup"><span data-stu-id="5197e-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="5197e-660">Yapılandırma sağlayıcılarından yapılandırmayı <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>okuma.</span><span class="sxs-lookup"><span data-stu-id="5197e-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="5197e-661">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="5197e-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="5197e-662">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="5197e-662">Application key (name)</span></span>

<span data-ttu-id="5197e-663">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği, ana bilgisayar inşaatı sırasında ana bilgisayar yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="5197e-664">Değeri açıkça ayarlamak için [HostDefaults.ApplicationKey:'i](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="5197e-665">**Anahtar**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="5197e-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5197e-666">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-666">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-667">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="5197e-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="5197e-668">**Set kullanarak:**`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="5197e-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="5197e-669">**Çevre değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5197e-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="5197e-670">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="5197e-670">Content root</span></span>

<span data-ttu-id="5197e-671">Bu ayar, ana bilgisayarın içerik dosyalarını aramaya nerede başlayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="5197e-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="5197e-672">**Anahtar**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5197e-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5197e-673">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-673">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-674">**Varsayılan**: Varsayılan olarak uygulama derlemesinin bulunduğu klasöre iner.</span><span class="sxs-lookup"><span data-stu-id="5197e-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="5197e-675">**Set kullanarak:**`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="5197e-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="5197e-676">**Çevre değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5197e-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5197e-677">Yol yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="5197e-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="5197e-678">Daha fazla bilgi için [temel bilgiler: İçerik kökü.](xref:fundamentals/index#content-root)</span><span class="sxs-lookup"><span data-stu-id="5197e-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="5197e-679">Ortam</span><span class="sxs-lookup"><span data-stu-id="5197e-679">Environment</span></span>

<span data-ttu-id="5197e-680">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5197e-681">**Anahtar**:`environment`</span><span class="sxs-lookup"><span data-stu-id="5197e-681">**Key**: `environment`</span></span>  
<span data-ttu-id="5197e-682">**Türü**:`string`</span><span class="sxs-lookup"><span data-stu-id="5197e-682">**Type**: `string`</span></span>  
<span data-ttu-id="5197e-683">**Varsayılan**:`Production`</span><span class="sxs-lookup"><span data-stu-id="5197e-683">**Default**: `Production`</span></span>  
<span data-ttu-id="5197e-684">**Set kullanarak:**`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="5197e-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="5197e-685">**Çevre değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5197e-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5197e-686">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-686">The environment can be set to any value.</span></span> <span data-ttu-id="5197e-687">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="5197e-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5197e-688">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5197e-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="5197e-689">YapılandırmaHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="5197e-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="5197e-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ana <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> bilgisayar için <xref:Microsoft.Extensions.Configuration.IConfiguration> bir oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="5197e-691">Ana bilgisayar yapılandırması, uygulamanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> yapı işleminde kullanım için başlatma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="5197e-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5197e-693">Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5197e-694">Hiçbir sağlayıcı varsayılan olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="5197e-694">No providers are included by default.</span></span> <span data-ttu-id="5197e-695">Aşağıdakiler de dahil olmak üzere, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>uygulamanın gerektirdiği yapılandırma sağlayıcılarını açıkça belirtmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="5197e-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="5197e-696">Dosya yapılandırması (örneğin, bir *hostsettings.json* dosyasından).</span><span class="sxs-lookup"><span data-stu-id="5197e-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="5197e-697">Çevre değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="5197e-697">Environment variable configuration.</span></span>
* <span data-ttu-id="5197e-698">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="5197e-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="5197e-699">Diğer gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="5197e-699">Any other required configuration providers.</span></span>

<span data-ttu-id="5197e-700">Ana bilgisayar dosya yapılandırması, uygulamanın temel yolunu `SetBasePath` belirterek ve ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrıyla etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="5197e-701">Örnek uygulama bir JSON dosyası, *hostsettings.json*ve dosyanın ana bilgisayar yapılandırma ayarlarını tüketmek için aramalar <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="5197e-702">Ana bilgisayara [ortam değişkenyapılandırması](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için ana bilgisayar oluşturucuyu arayın. <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*></span><span class="sxs-lookup"><span data-stu-id="5197e-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="5197e-703">`AddEnvironmentVariables`isteğe bağlı kullanıcı tanımlı öneki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5197e-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="5197e-704">Örnek uygulama bir önek `PREFIX_`kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="5197e-705">Çevre değişkenleri okunduğunda önek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5197e-706">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, ortam `PREFIX_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="5197e-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5197e-707">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya bir `dotnet run`uygulamayı çalıştırırken geliştirme *sırasında, özellikler/launchSettings.json* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="5197e-708">[Visual Studio Code'da](https://code.visualstudio.com/)ortam değişkenleri geliştirme sırasında *.vscode/launch.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="5197e-709">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5197e-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5197e-710">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağrılar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>eklenmez.</span><span class="sxs-lookup"><span data-stu-id="5197e-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="5197e-711">Komut satırı yapılandırması, komut satırı bağımsız değişkenlerini önceki yapılandırma sağlayıcıları tarafından sağlanan yapılandırmayı geçersiz kılmak için en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="5197e-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5197e-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="5197e-713">Ek yapılandırma [applicationName](#application-key-name) ve [contentRoot](#content-root) tuşları ile sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="5197e-714">Örnek `HostBuilder` yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>kullanarak:</span><span class="sxs-lookup"><span data-stu-id="5197e-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="5197e-715">YapılandırmaAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5197e-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5197e-716">Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostBuilder> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uygulama çağırılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5197e-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="5197e-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>uygulama <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="5197e-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5197e-719">Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="5197e-720">Tarafından <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) kullanılabilir ve <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="5197e-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="5197e-721">Uygulama [yapılandırması, ConfigureHostConfiguration](#configurehostconfiguration)tarafından sağlanan ana bilgisayar yapılandırması otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="5197e-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="5197e-722">Örnek uygulama <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>yapılandırması kullanarak:</span><span class="sxs-lookup"><span data-stu-id="5197e-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="5197e-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5197e-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="5197e-724">*ayarları. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="5197e-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="5197e-725">*ayarları. Üretim.json*:</span><span class="sxs-lookup"><span data-stu-id="5197e-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="5197e-726">Ayarlar dosyalarını çıktı dizinine taşımak için, proje dosyasındaki ayarlar dosyalarını [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="5197e-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="5197e-727">Örnek uygulama JSON uygulama ayarları dosyalarını ve *hostsettings.json'u* aşağıdaki `<Content>` öğeyle hareket ettirir:</span><span class="sxs-lookup"><span data-stu-id="5197e-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="5197e-728"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gibi yapılandırma uzantısı yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="5197e-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="5197e-729">Uygulama [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmadığı sürece, bu paketler in core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="5197e-730">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5197e-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="5197e-731">YapılandırmaHizmetleri</span><span class="sxs-lookup"><span data-stu-id="5197e-731">ConfigureServices</span></span>

<span data-ttu-id="5197e-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) konteynerine hizmetler ekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5197e-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5197e-734">Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="5197e-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="5197e-735">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="5197e-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="5197e-736">[Örnek uygulama,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` `LifetimeEventsHostedService`yaşam boyu olaylar için bir hizmet ve zamanlanmış arka `TimedHostedService`plan görevi, uygulamaya eklemek için uzantı yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="5197e-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="5197e-737">YapılandırmaLogging</span><span class="sxs-lookup"><span data-stu-id="5197e-737">ConfigureLogging</span></span>

<span data-ttu-id="5197e-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>sağlanan <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırma için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="5197e-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="5197e-740">UseConsoleÖmür Boyu</span><span class="sxs-lookup"><span data-stu-id="5197e-740">UseConsoleLifetime</span></span>

<span data-ttu-id="5197e-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'i dinler ve kapatma işlemini başlatmak için çağrıda bulunur. <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*></span><span class="sxs-lookup"><span data-stu-id="5197e-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="5197e-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .</span><span class="sxs-lookup"><span data-stu-id="5197e-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="5197e-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan ömür boyu uygulama olarak önceden kaydedilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5197e-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="5197e-744">Son ömür boyu kayıtlı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5197e-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="5197e-745">Konteyner yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5197e-745">Container configuration</span></span>

<span data-ttu-id="5197e-746">Diğer kapsayıcılara takMayı desteklemek için, <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>ana bilgisayar bir .</span><span class="sxs-lookup"><span data-stu-id="5197e-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="5197e-747">Bir fabrika sağlamak DI konteyner kaydının bir parçası değildir, ancak bunun yerine beton DI konteyner oluşturmak için kullanılan bir ana bilgisayardır.</span><span class="sxs-lookup"><span data-stu-id="5197e-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="5197e-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder),&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) uygulamanın servis sağlayıcısını oluşturmak için kullanılan varsayılan fabrikayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="5197e-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="5197e-749">Özel kapsayıcı yapılandırmayöntemi <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> tarafından yönetilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="5197e-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>kapsayıcıyı temel ana bilgisayar API'sinin üzerine yapılandırmak için güçlü bir şekilde yazılmamış bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="5197e-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>katkı sonuçları ile birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5197e-752">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="5197e-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="5197e-753">Bir servis konteyner fabrikası sağlayın:</span><span class="sxs-lookup"><span data-stu-id="5197e-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="5197e-754">Fabrikayı kullanın ve uygulama için özel servis konteynerini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="5197e-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="5197e-755">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="5197e-755">Extensibility</span></span>

<span data-ttu-id="5197e-756">Ana bilgisayar genişletilebilirliği uzantısı yöntemleri <xref:Microsoft.Extensions.Hosting.IHostBuilder>ile gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="5197e-757">Aşağıdaki örnek, uzantı yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> [timedHostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneğinde gösterildiği bir <xref:fundamentals/host/hosted-services>uygulamayı nasıl genişletir gösteriş olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5197e-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="5197e-758">Bir uygulama, `UseHostedService` barındırılan hizmeti kaydetmek için `T`uzantı yöntemini belirler:</span><span class="sxs-lookup"><span data-stu-id="5197e-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="5197e-759">Ev sahibini yönetme</span><span class="sxs-lookup"><span data-stu-id="5197e-759">Manage the host</span></span>

<span data-ttu-id="5197e-760">Uygulama, <xref:Microsoft.Extensions.Hosting.IHost> hizmet konteynerine <xref:Microsoft.Extensions.Hosting.IHostedService> kayıtlı uygulamaların başlatılmasından ve durdurulmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="5197e-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5197e-761">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5197e-761">Run</span></span>

<span data-ttu-id="5197e-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırAn ve ana bilgisayar kapatılana kadar arama iş parçacığı engeller:</span><span class="sxs-lookup"><span data-stu-id="5197e-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="5197e-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-763">RunAsync</span></span>

<span data-ttu-id="5197e-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür:</span><span class="sxs-lookup"><span data-stu-id="5197e-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="5197e-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-765">RunConsoleAsync</span></span>

<span data-ttu-id="5197e-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="5197e-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="5197e-767">Başlat ve DurdurAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-767">Start and StopAsync</span></span>

<span data-ttu-id="5197e-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="5197e-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="5197e-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5197e-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="5197e-770">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="5197e-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="5197e-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="5197e-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="5197e-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="5197e-773">Bekleme Kapatma</span><span class="sxs-lookup"><span data-stu-id="5197e-773">WaitForShutdown</span></span>

<span data-ttu-id="5197e-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><xref:Microsoft.Extensions.Hosting.IHostLifetime>(Ctrl <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM için dinler) gibi) `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` aracılığıyla tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="5197e-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="5197e-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır. <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*></span><span class="sxs-lookup"><span data-stu-id="5197e-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="5197e-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5197e-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="5197e-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="5197e-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="5197e-778">Dış kontrol</span><span class="sxs-lookup"><span data-stu-id="5197e-778">External control</span></span>

<span data-ttu-id="5197e-779">Ana bilgisayar dış kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="5197e-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5197e-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>kadar bekleyen başında denir.</span><span class="sxs-lookup"><span data-stu-id="5197e-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5197e-781">Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="5197e-782">IHostingEnvironment arayüzü</span><span class="sxs-lookup"><span data-stu-id="5197e-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="5197e-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5197e-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="5197e-784">Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> kullanın:</span><span class="sxs-lookup"><span data-stu-id="5197e-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="5197e-785">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5197e-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="5197e-786">IApplicationLifetime arayüzü</span><span class="sxs-lookup"><span data-stu-id="5197e-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="5197e-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>zarif kapatma istekleri de dahil olmak üzere, başlatma ve kapatma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="5197e-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="5197e-788">Arabirimdeki üç özellik, başlatma ve <xref:System.Action> kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="5197e-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="5197e-789">İptal Jetonu</span><span class="sxs-lookup"><span data-stu-id="5197e-789">Cancellation Token</span></span> | <span data-ttu-id="5197e-790">&#8230; tetiklenir</span><span class="sxs-lookup"><span data-stu-id="5197e-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="5197e-791">Ev sahibi tamamen başladı.</span><span class="sxs-lookup"><span data-stu-id="5197e-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="5197e-792">Ev sahibi zarif bir kapatma tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="5197e-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="5197e-793">Tüm istekler işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="5197e-793">All requests should be processed.</span></span> <span data-ttu-id="5197e-794">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="5197e-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="5197e-795">Ev sahibi zarif bir kapatma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="5197e-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="5197e-796">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="5197e-796">Requests may still be processing.</span></span> <span data-ttu-id="5197e-797">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="5197e-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="5197e-798">Yapıcı herhangi bir <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> sınıfa hizmet enjekte.</span><span class="sxs-lookup"><span data-stu-id="5197e-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="5197e-799">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) olayları kaydetmek için `LifetimeEventsHostedService` bir <xref:Microsoft.Extensions.Hosting.IHostedService> sınıf (bir uygulama) içine yapıcı enjeksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="5197e-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="5197e-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5197e-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="5197e-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını talep ediyor.</span><span class="sxs-lookup"><span data-stu-id="5197e-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="5197e-802">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="5197e-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5197e-803">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5197e-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
