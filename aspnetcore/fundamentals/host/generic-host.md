---
title: .NET genel ana bilgisayar
author: rick-anderson
description: Uygulama başlatma ve ömür yönetiminden sorumlu .NET Core genel ana bilgisayarı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 59cfae9ff619f8de894686c4b773d66e5cbe10ad
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754742"
---
# <a name="net-generic-host"></a><span data-ttu-id="9da92-103">.NET genel ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="9da92-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9da92-104">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9da92-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="9da92-105">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="9da92-105">Host definition</span></span>

<span data-ttu-id="9da92-106">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="9da92-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9da92-107">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="9da92-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="9da92-108">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="9da92-108">Logging</span></span>
* <span data-ttu-id="9da92-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9da92-109">Configuration</span></span>
* <span data-ttu-id="9da92-110">`IHostedService` uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="9da92-110">`IHostedService` implementations</span></span>

<span data-ttu-id="9da92-111">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="9da92-112">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="9da92-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9da92-113">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="9da92-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9da92-114">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="9da92-114">Set up a host</span></span>

<span data-ttu-id="9da92-115">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="9da92-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9da92-116">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9da92-116">The `Main` method:</span></span>

* <span data-ttu-id="9da92-117">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9da92-118">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="9da92-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9da92-119">ASP.NET Core Web şablonları, bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9da92-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="9da92-120">Aşağıdaki kod, dı kapsayıcısına eklenen bir uygulamayla HTTP olmayan bir iş yükü oluşturur `IHostedService` .</span><span class="sxs-lookup"><span data-stu-id="9da92-120">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="9da92-121">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="9da92-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9da92-122">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9da92-123">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9da92-124">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="9da92-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9da92-125">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="9da92-125">Default builder settings</span></span>

<span data-ttu-id="9da92-126"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9da92-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9da92-127">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9da92-128">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9da92-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="9da92-129">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9da92-130">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-130">Command-line arguments.</span></span>
* <span data-ttu-id="9da92-131">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9da92-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="9da92-132">\* Üzerindeappsettings.js\*.</span><span class="sxs-lookup"><span data-stu-id="9da92-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="9da92-133">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9da92-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9da92-134">Uygulama ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) `Development` .</span><span class="sxs-lookup"><span data-stu-id="9da92-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9da92-135">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-135">Environment variables.</span></span>
  * <span data-ttu-id="9da92-136">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-136">Command-line arguments.</span></span>
* <span data-ttu-id="9da92-137">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="9da92-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9da92-138">Konsol</span><span class="sxs-lookup"><span data-stu-id="9da92-138">Console</span></span>
  * <span data-ttu-id="9da92-139">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9da92-139">Debug</span></span>
  * <span data-ttu-id="9da92-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="9da92-140">EventSource</span></span>
  * <span data-ttu-id="9da92-141">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="9da92-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9da92-142">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9da92-143">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9da92-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9da92-144">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9da92-145">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9da92-146">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="9da92-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9da92-147">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9da92-148">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="9da92-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9da92-149">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-149">Enables IIS integration.</span></span> <span data-ttu-id="9da92-150">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="9da92-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9da92-151">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9da92-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9da92-152">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="9da92-152">Framework-provided services</span></span>

<span data-ttu-id="9da92-153">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="9da92-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9da92-154">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9da92-155">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9da92-156">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="9da92-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9da92-157">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="9da92-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9da92-158">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="9da92-159"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9da92-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9da92-160">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="9da92-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9da92-161">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="9da92-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9da92-162">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="9da92-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9da92-163">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-163">IHostLifetime</span></span>

<span data-ttu-id="9da92-164"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="9da92-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9da92-165">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-165">The last implementation registered is used.</span></span>

<span data-ttu-id="9da92-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="9da92-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9da92-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9da92-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9da92-168"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9da92-169">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9da92-170">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="9da92-170">IHostEnvironment</span></span>

<span data-ttu-id="9da92-171"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9da92-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9da92-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9da92-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9da92-173">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9da92-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9da92-174">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="9da92-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="9da92-175">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [WebRootPath](#webroot)</span><span class="sxs-lookup"><span data-stu-id="9da92-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9da92-176">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9da92-176">Host configuration</span></span>

<span data-ttu-id="9da92-177">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="9da92-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9da92-178">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9da92-179">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9da92-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9da92-180">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9da92-181">`ConfigureHostConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9da92-182">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9da92-183">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9da92-184">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9da92-185">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9da92-186">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="9da92-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9da92-187">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9da92-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9da92-188">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9da92-188">App configuration</span></span>

<span data-ttu-id="9da92-189">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9da92-190">`ConfigureAppConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9da92-191">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9da92-192">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9da92-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9da92-193">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9da92-194">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9da92-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9da92-195">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="9da92-195">Settings for all app types</span></span>

<span data-ttu-id="9da92-196">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="9da92-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9da92-197">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9da92-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9da92-198">ApplicationName</span></span>

<span data-ttu-id="9da92-199">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9da92-200">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9da92-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9da92-201">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-201">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-202">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="9da92-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9da92-203">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9da92-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9da92-204">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9da92-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="9da92-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-205">ContentRoot</span></span>

<span data-ttu-id="9da92-206">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="9da92-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9da92-207">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="9da92-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9da92-208">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9da92-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9da92-209">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-209">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-210">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="9da92-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9da92-211">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9da92-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9da92-212">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9da92-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9da92-213">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9da92-213">For more information, see:</span></span>

* [<span data-ttu-id="9da92-214">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="9da92-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9da92-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9da92-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9da92-216">EnvironmentName</span></span>

<span data-ttu-id="9da92-217">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9da92-218">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="9da92-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9da92-219">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9da92-220">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9da92-220">**Key**: `environment`</span></span>  
<span data-ttu-id="9da92-221">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-221">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-222">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9da92-222">**Default**: `Production`</span></span>  
<span data-ttu-id="9da92-223">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9da92-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9da92-224">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9da92-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9da92-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9da92-225">ShutdownTimeout</span></span>

<span data-ttu-id="9da92-226">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9da92-227">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="9da92-227">The default value is five seconds.</span></span>  <span data-ttu-id="9da92-228">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="9da92-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="9da92-229">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="9da92-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9da92-230">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9da92-231">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="9da92-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9da92-232">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="9da92-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9da92-233">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="9da92-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9da92-234">**Anahtar**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9da92-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9da92-235">**Şunu yazın**: `int`</span><span class="sxs-lookup"><span data-stu-id="9da92-235">**Type**: `int`</span></span>  
<span data-ttu-id="9da92-236">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="9da92-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9da92-237">**Ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9da92-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9da92-238">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="9da92-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9da92-239">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="9da92-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="9da92-240">Değişiklik sırasında uygulama yapılandırması yeniden yüklemeyi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="9da92-240">Disable app configuration reload on change</span></span>

<span data-ttu-id="9da92-241">[Varsayılan](xref:fundamentals/configuration/index#default)olarak, *appsettings.json* ve appSettingsappsettings.js *. { Ortam}. JSON* , dosya değiştiğinde yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-241">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="9da92-242">ASP.NET Core 5,0 veya sonraki bir sürümde yeniden yükleme davranışını devre dışı bırakmak için `hostBuilder:reloadConfigOnChange` anahtarı olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="9da92-242">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="9da92-243">**Anahtar**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9da92-243">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9da92-244">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-244">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-245">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="9da92-245">**Default**: `true`</span></span>  
<span data-ttu-id="9da92-246">**Komut satırı bağımsız değişkeni**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9da92-246">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9da92-247">**Ortam değişkeni**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9da92-247">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="9da92-248">İki nokta ( `:` ) ayırıcısı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="9da92-248">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="9da92-249">Daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9da92-249">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="9da92-250">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="9da92-250">Settings for web apps</span></span>

<span data-ttu-id="9da92-251">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9da92-251">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9da92-252">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-252">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9da92-253">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-253">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9da92-254">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="9da92-254">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9da92-255">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9da92-255">CaptureStartupErrors</span></span>

<span data-ttu-id="9da92-256">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="9da92-256">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9da92-257">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da92-257">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9da92-258">**Anahtar**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9da92-258">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9da92-259">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-259">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-260">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="9da92-260">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9da92-261">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9da92-261">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9da92-262">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="9da92-262">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9da92-263">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9da92-263">DetailedErrors</span></span>

<span data-ttu-id="9da92-264">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="9da92-264">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9da92-265">**Anahtar**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9da92-265">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9da92-266">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-266">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-267">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9da92-267">**Default**: `false`</span></span>  
<span data-ttu-id="9da92-268">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9da92-268">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9da92-269">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9da92-270">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9da92-270">HostingStartupAssemblies</span></span>

<span data-ttu-id="9da92-271">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9da92-271">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9da92-272">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="9da92-272">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9da92-273">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-273">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9da92-274">**Anahtar**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9da92-274">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9da92-275">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-275">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-276">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9da92-276">**Default**: Empty string</span></span>  
<span data-ttu-id="9da92-277">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9da92-277">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9da92-278">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-278">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9da92-279">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9da92-279">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9da92-280">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9da92-280">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9da92-281">**Anahtar**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9da92-281">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9da92-282">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-282">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-283">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9da92-283">**Default**: Empty string</span></span>  
<span data-ttu-id="9da92-284">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9da92-284">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9da92-285">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9da92-286">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9da92-286">HTTPS_Port</span></span>

<span data-ttu-id="9da92-287">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="9da92-287">The HTTPS redirect port.</span></span> <span data-ttu-id="9da92-288">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-288">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9da92-289">**Anahtar**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9da92-289">**Key**: `https_port`</span></span>  
<span data-ttu-id="9da92-290">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-290">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-291">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-291">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9da92-292">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9da92-292">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9da92-293">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-293">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9da92-294">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="9da92-294">PreferHostingUrls</span></span>

<span data-ttu-id="9da92-295">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="9da92-295">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9da92-296">**Anahtar**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9da92-296">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9da92-297">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-298">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="9da92-298">**Default**: `true`</span></span>  
<span data-ttu-id="9da92-299">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9da92-299">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9da92-300">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="9da92-300">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9da92-301">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="9da92-301">PreventHostingStartup</span></span>

<span data-ttu-id="9da92-302">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="9da92-302">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9da92-303">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9da92-303">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9da92-304">**Anahtar**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9da92-304">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9da92-305">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-305">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-306">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9da92-306">**Default**: `false`</span></span>  
<span data-ttu-id="9da92-307">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9da92-307">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9da92-308">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-308">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9da92-309">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9da92-309">StartupAssembly</span></span>

<span data-ttu-id="9da92-310">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9da92-310">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9da92-311">**Anahtar**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9da92-311">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9da92-312">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-312">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-313">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="9da92-313">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9da92-314">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9da92-314">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9da92-315">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="9da92-315">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9da92-316">`UseStartup` bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="9da92-316">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9da92-317">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="9da92-317">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9da92-318">URL’ler</span><span class="sxs-lookup"><span data-stu-id="9da92-318">URLs</span></span>

<span data-ttu-id="9da92-319">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-319">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9da92-320">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9da92-320">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9da92-321">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="9da92-321">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9da92-322">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9da92-322">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9da92-323">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="9da92-323">Supported formats vary among servers.</span></span>

<span data-ttu-id="9da92-324">**Anahtar**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9da92-324">**Key**: `urls`</span></span>  
<span data-ttu-id="9da92-325">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-325">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-326">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9da92-326">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9da92-327">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9da92-327">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9da92-328">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="9da92-328">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9da92-329">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9da92-329">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9da92-330">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9da92-330">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9da92-331">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-331">WebRoot</span></span>

<span data-ttu-id="9da92-332">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="9da92-332">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="9da92-333">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-333">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="9da92-334">**Anahtar**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9da92-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="9da92-335">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-335">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-336">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="9da92-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9da92-337">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9da92-337">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="9da92-338">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9da92-338">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9da92-339">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9da92-339">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9da92-340">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9da92-340">For more information, see:</span></span>

* [<span data-ttu-id="9da92-341">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="9da92-341">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9da92-342">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-342">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9da92-343">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="9da92-343">Manage the host lifetime</span></span>

<span data-ttu-id="9da92-344"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="9da92-344">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9da92-345">Bu yöntemler  <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="9da92-345">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9da92-346">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="9da92-346">Run</span></span>

<span data-ttu-id="9da92-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9da92-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9da92-348">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-348">RunAsync</span></span>

<span data-ttu-id="9da92-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9da92-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9da92-350">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-350">RunConsoleAsync</span></span>

<span data-ttu-id="9da92-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9da92-352">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="9da92-352">Start</span></span>

<span data-ttu-id="9da92-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="9da92-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9da92-354">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-354">StartAsync</span></span>

<span data-ttu-id="9da92-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9da92-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9da92-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9da92-357">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-357">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9da92-358">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-358">StopAsync</span></span>

<span data-ttu-id="9da92-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da92-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9da92-360">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="9da92-360">WaitForShutdown</span></span>

<span data-ttu-id="9da92-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9da92-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9da92-362">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-362">WaitForShutdownAsync</span></span>

<span data-ttu-id="9da92-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9da92-364">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="9da92-364">External control</span></span>

<span data-ttu-id="9da92-365">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="9da92-365">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9da92-366">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9da92-366">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="9da92-367">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="9da92-367">Host definition</span></span>

<span data-ttu-id="9da92-368">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="9da92-368">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9da92-369">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="9da92-369">Dependency injection (DI)</span></span>
* <span data-ttu-id="9da92-370">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="9da92-370">Logging</span></span>
* <span data-ttu-id="9da92-371">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9da92-371">Configuration</span></span>
* <span data-ttu-id="9da92-372">`IHostedService` uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="9da92-372">`IHostedService` implementations</span></span>

<span data-ttu-id="9da92-373">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-373">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="9da92-374">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="9da92-374">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9da92-375">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="9da92-375">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9da92-376">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="9da92-376">Set up a host</span></span>

<span data-ttu-id="9da92-377">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="9da92-377">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9da92-378">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9da92-378">The `Main` method:</span></span>

* <span data-ttu-id="9da92-379">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-379">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9da92-380">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="9da92-380">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9da92-381">ASP.NET Core Web şablonları, genel bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9da92-381">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="9da92-382">Aşağıdaki kod, HTTP olmayan iş yükünü kullanan genel bir konak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9da92-382">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="9da92-383">`IHostedService`Uygulama, dı kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="9da92-383">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="9da92-384">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="9da92-384">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9da92-385">Yukarıdaki kod ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da92-385">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="9da92-386">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9da92-387">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9da92-388">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="9da92-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9da92-389">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="9da92-389">Default builder settings</span></span>

<span data-ttu-id="9da92-390"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9da92-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="9da92-391">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="9da92-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="9da92-392">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9da92-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="9da92-393">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9da92-394">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-394">Command-line arguments.</span></span>
* <span data-ttu-id="9da92-395">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9da92-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="9da92-396">\* Üzerindeappsettings.js\*.</span><span class="sxs-lookup"><span data-stu-id="9da92-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="9da92-397">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9da92-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9da92-398">Uygulama ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) `Development` .</span><span class="sxs-lookup"><span data-stu-id="9da92-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9da92-399">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-399">Environment variables.</span></span>
  * <span data-ttu-id="9da92-400">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-400">Command-line arguments.</span></span>
* <span data-ttu-id="9da92-401">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="9da92-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9da92-402">Konsol</span><span class="sxs-lookup"><span data-stu-id="9da92-402">Console</span></span>
  * <span data-ttu-id="9da92-403">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9da92-403">Debug</span></span>
  * <span data-ttu-id="9da92-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="9da92-404">EventSource</span></span>
  * <span data-ttu-id="9da92-405">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="9da92-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9da92-406">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9da92-407">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9da92-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9da92-408">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9da92-409">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9da92-410">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="9da92-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9da92-411">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9da92-412">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="9da92-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9da92-413">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-413">Enables IIS integration.</span></span> <span data-ttu-id="9da92-414">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="9da92-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9da92-415">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9da92-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9da92-416">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="9da92-416">Framework-provided services</span></span>

<span data-ttu-id="9da92-417">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="9da92-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9da92-418">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9da92-419">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9da92-420">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="9da92-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9da92-421">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="9da92-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9da92-422">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="9da92-423"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9da92-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9da92-424">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="9da92-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9da92-425">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="9da92-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9da92-426">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="9da92-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9da92-427">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-427">IHostLifetime</span></span>

<span data-ttu-id="9da92-428"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="9da92-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9da92-429">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-429">The last implementation registered is used.</span></span>

<span data-ttu-id="9da92-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="9da92-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9da92-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9da92-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9da92-432"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="9da92-433">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9da92-434">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="9da92-434">IHostEnvironment</span></span>

<span data-ttu-id="9da92-435"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9da92-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9da92-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9da92-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9da92-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9da92-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9da92-438">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="9da92-438">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="9da92-439">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [WebRootPath](#webroot)</span><span class="sxs-lookup"><span data-stu-id="9da92-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9da92-440">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9da92-440">Host configuration</span></span>

<span data-ttu-id="9da92-441">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="9da92-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9da92-442">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="9da92-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="9da92-443">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9da92-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9da92-444">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="9da92-445">`ConfigureHostConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9da92-446">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9da92-447">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9da92-448">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9da92-449">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9da92-450">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="9da92-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9da92-451">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9da92-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9da92-452">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9da92-452">App configuration</span></span>

<span data-ttu-id="9da92-453">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9da92-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9da92-454">`ConfigureAppConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9da92-455">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9da92-456">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9da92-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9da92-457">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9da92-458">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9da92-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9da92-459">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="9da92-459">Settings for all app types</span></span>

<span data-ttu-id="9da92-460">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="9da92-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9da92-461">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9da92-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9da92-462">ApplicationName</span></span>

<span data-ttu-id="9da92-463">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9da92-464">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9da92-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9da92-465">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-465">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-466">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="9da92-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9da92-467">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9da92-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9da92-468">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9da92-468">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="9da92-469">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-469">ContentRoot</span></span>

<span data-ttu-id="9da92-470">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="9da92-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9da92-471">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="9da92-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9da92-472">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9da92-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9da92-473">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-473">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-474">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="9da92-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9da92-475">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9da92-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9da92-476">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9da92-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9da92-477">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9da92-477">For more information, see:</span></span>

* [<span data-ttu-id="9da92-478">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="9da92-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9da92-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9da92-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9da92-480">EnvironmentName</span></span>

<span data-ttu-id="9da92-481">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9da92-482">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="9da92-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9da92-483">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9da92-484">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9da92-484">**Key**: `environment`</span></span>  
<span data-ttu-id="9da92-485">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-485">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-486">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9da92-486">**Default**: `Production`</span></span>  
<span data-ttu-id="9da92-487">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9da92-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9da92-488">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9da92-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9da92-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9da92-489">ShutdownTimeout</span></span>

<span data-ttu-id="9da92-490">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9da92-491">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="9da92-491">The default value is five seconds.</span></span>  <span data-ttu-id="9da92-492">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="9da92-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="9da92-493">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="9da92-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9da92-494">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9da92-495">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="9da92-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9da92-496">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="9da92-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9da92-497">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="9da92-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9da92-498">**Anahtar**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9da92-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9da92-499">**Şunu yazın**: `int`</span><span class="sxs-lookup"><span data-stu-id="9da92-499">**Type**: `int`</span></span>  
<span data-ttu-id="9da92-500">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="9da92-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9da92-501">**Ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9da92-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9da92-502">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="9da92-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9da92-503">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="9da92-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="9da92-504">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="9da92-504">Settings for web apps</span></span>

<span data-ttu-id="9da92-505">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9da92-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9da92-506">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9da92-507">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9da92-508">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="9da92-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9da92-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9da92-509">CaptureStartupErrors</span></span>

<span data-ttu-id="9da92-510">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="9da92-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9da92-511">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da92-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9da92-512">**Anahtar**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9da92-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9da92-513">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-514">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="9da92-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9da92-515">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9da92-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9da92-516">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="9da92-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9da92-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9da92-517">DetailedErrors</span></span>

<span data-ttu-id="9da92-518">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="9da92-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9da92-519">**Anahtar**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9da92-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9da92-520">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-521">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9da92-521">**Default**: `false`</span></span>  
<span data-ttu-id="9da92-522">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9da92-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9da92-523">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9da92-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9da92-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="9da92-525">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9da92-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9da92-526">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="9da92-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9da92-527">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9da92-528">**Anahtar**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9da92-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9da92-529">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-529">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-530">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9da92-530">**Default**: Empty string</span></span>  
<span data-ttu-id="9da92-531">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9da92-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9da92-532">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9da92-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9da92-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9da92-534">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9da92-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9da92-535">**Anahtar**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9da92-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9da92-536">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-536">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-537">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9da92-537">**Default**: Empty string</span></span>  
<span data-ttu-id="9da92-538">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9da92-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9da92-539">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9da92-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9da92-540">HTTPS_Port</span></span>

<span data-ttu-id="9da92-541">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="9da92-541">The HTTPS redirect port.</span></span> <span data-ttu-id="9da92-542">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9da92-543">**Anahtar**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9da92-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="9da92-544">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-544">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-545">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9da92-546">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9da92-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9da92-547">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9da92-548">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="9da92-548">PreferHostingUrls</span></span>

<span data-ttu-id="9da92-549">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="9da92-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9da92-550">**Anahtar**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9da92-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9da92-551">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-552">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="9da92-552">**Default**: `true`</span></span>  
<span data-ttu-id="9da92-553">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9da92-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9da92-554">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="9da92-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9da92-555">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="9da92-555">PreventHostingStartup</span></span>

<span data-ttu-id="9da92-556">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="9da92-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9da92-557">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9da92-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9da92-558">**Anahtar**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9da92-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9da92-559">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9da92-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9da92-560">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9da92-560">**Default**: `false`</span></span>  
<span data-ttu-id="9da92-561">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9da92-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9da92-562">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9da92-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9da92-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9da92-563">StartupAssembly</span></span>

<span data-ttu-id="9da92-564">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9da92-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9da92-565">**Anahtar**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9da92-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9da92-566">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-566">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-567">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="9da92-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9da92-568">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9da92-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9da92-569">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="9da92-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9da92-570">`UseStartup` bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="9da92-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9da92-571">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="9da92-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9da92-572">URL’ler</span><span class="sxs-lookup"><span data-stu-id="9da92-572">URLs</span></span>

<span data-ttu-id="9da92-573">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9da92-574">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9da92-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9da92-575">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="9da92-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9da92-576">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9da92-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9da92-577">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="9da92-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="9da92-578">**Anahtar**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9da92-578">**Key**: `urls`</span></span>  
<span data-ttu-id="9da92-579">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-579">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-580">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9da92-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9da92-581">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9da92-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9da92-582">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="9da92-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9da92-583">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9da92-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9da92-584">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9da92-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9da92-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-585">WebRoot</span></span>

<span data-ttu-id="9da92-586">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="9da92-586">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="9da92-587">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-587">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="9da92-588">**Anahtar**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9da92-588">**Key**: `webroot`</span></span>  
<span data-ttu-id="9da92-589">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-589">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-590">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="9da92-590">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9da92-591">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9da92-591">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="9da92-592">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9da92-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9da92-593">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9da92-593">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9da92-594">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9da92-594">For more information, see:</span></span>

* [<span data-ttu-id="9da92-595">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="9da92-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9da92-596">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9da92-596">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9da92-597">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="9da92-597">Manage the host lifetime</span></span>

<span data-ttu-id="9da92-598"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="9da92-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9da92-599">Bu yöntemler  <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="9da92-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9da92-600">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="9da92-600">Run</span></span>

<span data-ttu-id="9da92-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9da92-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9da92-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-602">RunAsync</span></span>

<span data-ttu-id="9da92-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9da92-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9da92-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-604">RunConsoleAsync</span></span>

<span data-ttu-id="9da92-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9da92-606">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="9da92-606">Start</span></span>

<span data-ttu-id="9da92-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="9da92-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9da92-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-608">StartAsync</span></span>

<span data-ttu-id="9da92-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9da92-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9da92-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9da92-611">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9da92-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-612">StopAsync</span></span>

<span data-ttu-id="9da92-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da92-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9da92-614">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="9da92-614">WaitForShutdown</span></span>

<span data-ttu-id="9da92-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9da92-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9da92-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="9da92-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9da92-618">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="9da92-618">External control</span></span>

<span data-ttu-id="9da92-619">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="9da92-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9da92-620">ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="9da92-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="9da92-621">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="9da92-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="9da92-622">Bu makalede <xref:Microsoft.Extensions.Hosting.HostBuilder> , http isteklerini işlemeyecek uygulamalar için kullanılan genel ana bilgisayar () ASP.NET Core ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9da92-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="9da92-623">Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="9da92-624">Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.</span><span class="sxs-lookup"><span data-stu-id="9da92-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="9da92-625">Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="9da92-626">Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="9da92-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9da92-627">Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.</span><span class="sxs-lookup"><span data-stu-id="9da92-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="9da92-628">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9da92-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9da92-629">Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal*kullanın.</span><span class="sxs-lookup"><span data-stu-id="9da92-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="9da92-630">Örneği bir içinde çalıştırmayın `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="9da92-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="9da92-631">Konsolu Visual Studio Code ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="9da92-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="9da92-632">Dosyasında *. vscode/launch.js* açın.</span><span class="sxs-lookup"><span data-stu-id="9da92-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="9da92-633">**.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="9da92-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="9da92-634">Değeri ya da olarak ayarlayın `externalTerminal` `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="9da92-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="9da92-635">Giriş</span><span class="sxs-lookup"><span data-stu-id="9da92-635">Introduction</span></span>

<span data-ttu-id="9da92-636">Genel konak kitaplığı ad alanında kullanılabilir <xref:Microsoft.Extensions.Hosting> ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="9da92-637">`Microsoft.Extensions.Hosting`Paket, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="9da92-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="9da92-638"><xref:Microsoft.Extensions.Hosting.IHostedService> , kod yürütmeye yönelik giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="9da92-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="9da92-639">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices içinde hizmet kaydı](#configureservices)sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="9da92-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="9da92-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> , <xref:Microsoft.Extensions.Hosting.IHostedService> konak başlatıldığında çağrılır ve <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ana bilgisayar düzgün bir şekilde kapandığında ters kayıt sırasında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9da92-641">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="9da92-641">Set up a host</span></span>

<span data-ttu-id="9da92-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> , kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="9da92-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="9da92-643">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="9da92-643">Options</span></span>

<span data-ttu-id="9da92-644"><xref:Microsoft.Extensions.Hosting.HostOptions> için seçenekleri yapılandırın <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="9da92-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="9da92-645">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="9da92-645">Shutdown timeout</span></span>

<span data-ttu-id="9da92-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9da92-647">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="9da92-647">The default value is five seconds.</span></span>

<span data-ttu-id="9da92-648">İçindeki aşağıdaki seçenek yapılandırması, `Program.Main` varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:</span><span class="sxs-lookup"><span data-stu-id="9da92-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="9da92-649">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="9da92-649">Default services</span></span>

<span data-ttu-id="9da92-650">Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="9da92-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="9da92-651">[Ortam](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="9da92-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="9da92-652">[Yapılandırma](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="9da92-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="9da92-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9da92-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="9da92-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9da92-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="9da92-655">[Seçenekler](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="9da92-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="9da92-656">[Günlüğe kaydetme](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="9da92-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9da92-657">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9da92-657">Host configuration</span></span>

<span data-ttu-id="9da92-658">Ana bilgisayar yapılandırması şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9da92-658">Host configuration is created by:</span></span>

* <span data-ttu-id="9da92-659"><xref:Microsoft.Extensions.Hosting.IHostBuilder> [İçerik kökünü](#content-root) ve [ortamı](#environment)ayarlamak için uzantı yöntemleri çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="9da92-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="9da92-660">İçindeki yapılandırma sağlayıcılarından yapılandırma okunuyor <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="9da92-661">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="9da92-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="9da92-662">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="9da92-662">Application key (name)</span></span>

<span data-ttu-id="9da92-663">[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="9da92-664">Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="9da92-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="9da92-665">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9da92-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9da92-666">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-666">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-667">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="9da92-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="9da92-668">Şunu **kullanarak ayarla**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="9da92-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="9da92-669">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9da92-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="9da92-670">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="9da92-670">Content root</span></span>

<span data-ttu-id="9da92-671">Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="9da92-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="9da92-672">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9da92-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9da92-673">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-673">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-674">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="9da92-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="9da92-675">Şunu **kullanarak ayarla**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="9da92-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="9da92-676">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9da92-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9da92-677">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="9da92-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="9da92-678">Daha fazla bilgi için bkz. [temel bilgiler: içerik kökü](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="9da92-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="9da92-679">Ortam</span><span class="sxs-lookup"><span data-stu-id="9da92-679">Environment</span></span>

<span data-ttu-id="9da92-680">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9da92-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9da92-681">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9da92-681">**Key**: `environment`</span></span>  
<span data-ttu-id="9da92-682">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9da92-682">**Type**: `string`</span></span>  
<span data-ttu-id="9da92-683">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9da92-683">**Default**: `Production`</span></span>  
<span data-ttu-id="9da92-684">Şunu **kullanarak ayarla**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="9da92-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="9da92-685">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9da92-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9da92-686">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-686">The environment can be set to any value.</span></span> <span data-ttu-id="9da92-687">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="9da92-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9da92-688">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="9da92-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="9da92-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="9da92-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>konak için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="9da92-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="9da92-691">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın derleme sürecinde kullanılmak üzere başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="9da92-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9da92-693">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9da92-694">Varsayılan olarak hiçbir sağlayıcı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="9da92-694">No providers are included by default.</span></span> <span data-ttu-id="9da92-695">Uygulamanın gerektirdiği her türlü yapılandırma sağlayıcısını açık bir şekilde belirtmeniz gerekir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , örneğin:</span><span class="sxs-lookup"><span data-stu-id="9da92-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="9da92-696">Dosya yapılandırması (örneğin, dosyadaki bir *hostsettings.js* ).</span><span class="sxs-lookup"><span data-stu-id="9da92-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="9da92-697">Ortam değişkeni yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="9da92-697">Environment variable configuration.</span></span>
* <span data-ttu-id="9da92-698">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="9da92-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="9da92-699">Diğer tüm gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="9da92-699">Any other required configuration providers.</span></span>

<span data-ttu-id="9da92-700">Konağın dosya yapılandırması, uygulamanın temel yolu ve `SetBasePath` ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="9da92-701">Örnek uygulama, \* üzerindehostsettings.js\*bir JSON dosyası ve <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> dosyanın ana bilgisayar yapılandırma ayarlarını kullanmak için çağrılar kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="9da92-702">Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak Oluşturucu ' ya çağrı yapın <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="9da92-703">`AddEnvironmentVariables` Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9da92-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="9da92-704">Örnek uygulama, öneki kullanır `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="9da92-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="9da92-705">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9da92-706">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, için ortam değişkeni değeri `PREFIX_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="9da92-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9da92-707">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya ile bir uygulama çalıştırırken geliştirme sırasında `dotnet run` , ortam değişkenleri dosyadaki *Özellikler/launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="9da92-708">[Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında dosya *üzerinde. vscode/launch.js* içinde ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="9da92-709">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9da92-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9da92-710">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağırarak eklenir <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="9da92-711">Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="9da92-712">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="9da92-712">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="9da92-713">Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="9da92-714">`HostBuilder`Kullanarak örnek yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="9da92-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="9da92-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9da92-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9da92-716">Uygulama yapılandırması, uygulama çağrısı yaparak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9da92-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="9da92-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>uygulama için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="9da92-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="9da92-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9da92-719">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9da92-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="9da92-720">Tarafından oluşturulan yapılandırma, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sonraki işlemler ve içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bölümünde mevcuttur <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="9da92-721">Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9da92-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="9da92-722">Kullanarak örnek uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="9da92-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="9da92-723">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="9da92-723">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="9da92-724">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="9da92-724">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="9da92-725">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="9da92-725">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="9da92-726">Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="9da92-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="9da92-727">Örnek uygulama, JSON uygulama ayarları dosyalarını ve *hostsettings.js* aşağıdaki öğeyle taşıdıkça `<Content>` :</span><span class="sxs-lookup"><span data-stu-id="9da92-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="9da92-728">Ve gibi yapılandırma uzantısı yöntemleri, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.Js](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) veMicrosoft.Extensions.Configuration gibi ek NuGet paketleri gerektirir [ . EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="9da92-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="9da92-729">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadıkça, bu paketlerin çekirdek [Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9da92-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="9da92-730">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9da92-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="9da92-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="9da92-731">ConfigureServices</span></span>

<span data-ttu-id="9da92-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9da92-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9da92-734">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="9da92-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="9da92-735">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="9da92-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="9da92-736">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `AddHostedService` yaşam süresi olayları, `LifetimeEventsHostedService` ve zamanlanan bir arka plan görevi için uygulamaya bir hizmet eklemek için genişletme yöntemini kullanır `TimedHostedService` :</span><span class="sxs-lookup"><span data-stu-id="9da92-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="9da92-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="9da92-737">ConfigureLogging</span></span>

<span data-ttu-id="9da92-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> belirtilen yapılandırmayı yapılandırmak için bir temsilci ekler <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9da92-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="9da92-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="9da92-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="9da92-740">UseConsoleLifetime</span></span>

<span data-ttu-id="9da92-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="9da92-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="9da92-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan yaşam süresi uygulamasına önceden kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="9da92-744">Kaydedilen son yaşam süresi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da92-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="9da92-745">Kapsayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9da92-745">Container configuration</span></span>

<span data-ttu-id="9da92-746">Diğer kapsayıcıları takmayı desteklemek için ana bilgisayar bir kabul edebilir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="9da92-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="9da92-747">Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır.</span><span class="sxs-lookup"><span data-stu-id="9da92-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="9da92-748">[Useserviceproviderfactory (ıvıceproviderfactory &lt; tcontainerbuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="9da92-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="9da92-749">Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemiyle yönetilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="9da92-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9da92-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="9da92-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9da92-752">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="9da92-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="9da92-753">Hizmet kapsayıcısı fabrikası sağlama:</span><span class="sxs-lookup"><span data-stu-id="9da92-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="9da92-754">Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="9da92-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="9da92-755">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="9da92-755">Extensibility</span></span>

<span data-ttu-id="9da92-756">Konak genişletilebilirliği, üzerindeki genişletme yöntemleriyle gerçekleştirilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9da92-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="9da92-757">Aşağıdaki örnek, bir genişletme yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> ' de gösterilen [Timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir uygulamayı nasıl genişlettiğini gösterir <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="9da92-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="9da92-758">Uygulama, `UseHostedService` geçirilen barındırılan hizmeti kaydetmek için uzantı yöntemi oluşturur `T` :</span><span class="sxs-lookup"><span data-stu-id="9da92-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="9da92-759">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="9da92-759">Manage the host</span></span>

<span data-ttu-id="9da92-760"><xref:Microsoft.Extensions.Hosting.IHost>Uygulama, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı olan uygulamaları başlatıp durdurmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="9da92-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9da92-761">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="9da92-761">Run</span></span>

<span data-ttu-id="9da92-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="9da92-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="9da92-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-763">RunAsync</span></span>

<span data-ttu-id="9da92-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="9da92-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="9da92-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-765">RunConsoleAsync</span></span>

<span data-ttu-id="9da92-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="9da92-767">Başlangıç ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-767">Start and StopAsync</span></span>

<span data-ttu-id="9da92-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="9da92-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="9da92-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da92-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="9da92-770">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="9da92-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="9da92-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="9da92-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> uygulamayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="9da92-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="9da92-773">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="9da92-773">WaitForShutdown</span></span>

<span data-ttu-id="9da92-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, (örneğin, <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterim dinler) ile tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="9da92-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="9da92-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> çağırır <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="9da92-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9da92-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="9da92-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9da92-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="9da92-778">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="9da92-778">External control</span></span>

<span data-ttu-id="9da92-779">Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:</span><span class="sxs-lookup"><span data-stu-id="9da92-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9da92-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="9da92-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9da92-781">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="9da92-782">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="9da92-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="9da92-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9da92-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="9da92-784">Özelliklerini ve uzantı yöntemlerini kullanmak üzere sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> :</span><span class="sxs-lookup"><span data-stu-id="9da92-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="9da92-785">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9da92-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="9da92-786">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="9da92-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="9da92-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> düzgün kapanma istekleri de dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="9da92-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="9da92-788">Arabirimdeki üç özellik, <xref:System.Action> Başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="9da92-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="9da92-789">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="9da92-789">Cancellation Token</span></span> | <span data-ttu-id="9da92-790">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="9da92-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="9da92-791">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="9da92-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="9da92-792">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="9da92-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="9da92-793">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9da92-793">All requests should be processed.</span></span> <span data-ttu-id="9da92-794">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="9da92-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="9da92-795">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="9da92-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="9da92-796">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="9da92-796">Requests may still be processing.</span></span> <span data-ttu-id="9da92-797">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="9da92-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="9da92-798">Constructor- <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmeti herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9da92-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="9da92-799">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `LifetimeEventsHostedService` olayları kaydetmek için bir sınıfa (bir uygulama) ekleme oluşturucusunu kullanır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="9da92-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="9da92-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9da92-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="9da92-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="9da92-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="9da92-802">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır `Shutdown` :</span><span class="sxs-lookup"><span data-stu-id="9da92-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9da92-803">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9da92-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
