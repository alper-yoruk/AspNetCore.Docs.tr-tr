---
title: ASP.NET Core .NET genel ana bilgisayarı
author: rick-anderson
description: ASP.NET Core uygulamalarında .NET Core genel ana bilgisayarı kullanın.  Genel ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 263c7713166005dfdec8ede6bfa9b03b730dede7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96035820"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="9d875-104">ASP.NET Core .NET genel ana bilgisayarı</span><span class="sxs-lookup"><span data-stu-id="9d875-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9d875-105">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9d875-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="9d875-106">Bu konuda, ASP.NET Core .NET genel Host kullanımı hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="9d875-107">Konsol uygulamalarında .NET genel ana bilgisayarı kullanma hakkında bilgi için bkz. [.NET genel ana bilgisayar](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="9d875-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="9d875-108">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="9d875-108">Host definition</span></span>

<span data-ttu-id="9d875-109">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="9d875-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9d875-110">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="9d875-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="9d875-111">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="9d875-111">Logging</span></span>
* <span data-ttu-id="9d875-112">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9d875-112">Configuration</span></span>
* <span data-ttu-id="9d875-113">`IHostedService` uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="9d875-113">`IHostedService` implementations</span></span>

<span data-ttu-id="9d875-114">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="9d875-115">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="9d875-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9d875-116">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="9d875-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9d875-117">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="9d875-117">Set up a host</span></span>

<span data-ttu-id="9d875-118">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="9d875-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9d875-119">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d875-119">The `Main` method:</span></span>

* <span data-ttu-id="9d875-120">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9d875-121">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="9d875-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9d875-122">ASP.NET Core Web şablonları, bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9d875-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="9d875-123">Aşağıdaki kod, dı kapsayıcısına eklenen bir uygulamayla HTTP olmayan bir iş yükü oluşturur `IHostedService` .</span><span class="sxs-lookup"><span data-stu-id="9d875-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="9d875-124">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="9d875-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9d875-125">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9d875-126">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9d875-127">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="9d875-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9d875-128">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="9d875-128">Default builder settings</span></span>

<span data-ttu-id="9d875-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d875-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9d875-130">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9d875-131">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9d875-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="9d875-132">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9d875-133">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-133">Command-line arguments.</span></span>
* <span data-ttu-id="9d875-134">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9d875-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="9d875-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9d875-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="9d875-136">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9d875-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9d875-137">Uygulama ortamda çalıştırıldığında [Kullanıcı gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="9d875-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9d875-138">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-138">Environment variables.</span></span>
  * <span data-ttu-id="9d875-139">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-139">Command-line arguments.</span></span>
* <span data-ttu-id="9d875-140">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="9d875-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9d875-141">Konsol</span><span class="sxs-lookup"><span data-stu-id="9d875-141">Console</span></span>
  * <span data-ttu-id="9d875-142">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9d875-142">Debug</span></span>
  * <span data-ttu-id="9d875-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="9d875-143">EventSource</span></span>
  * <span data-ttu-id="9d875-144">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="9d875-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9d875-145">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9d875-146"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d875-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="9d875-147">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9d875-148">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9d875-149">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="9d875-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9d875-150">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9d875-151">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="9d875-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9d875-152">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-152">Enables IIS integration.</span></span> <span data-ttu-id="9d875-153">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="9d875-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9d875-154">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9d875-155">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="9d875-155">Framework-provided services</span></span>

<span data-ttu-id="9d875-156">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="9d875-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9d875-157">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9d875-158">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9d875-159">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="9d875-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9d875-160">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="9d875-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9d875-161">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="9d875-162"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9d875-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9d875-163">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="9d875-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9d875-164">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="9d875-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9d875-165">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="9d875-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9d875-166">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-166">IHostLifetime</span></span>

<span data-ttu-id="9d875-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="9d875-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9d875-168">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-168">The last implementation registered is used.</span></span>

<span data-ttu-id="9d875-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="9d875-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9d875-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9d875-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9d875-171"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9d875-172">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9d875-173">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="9d875-173">IHostEnvironment</span></span>

<span data-ttu-id="9d875-174"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9d875-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9d875-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9d875-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9d875-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9d875-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9d875-177">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="9d875-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="9d875-178">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [](#webroot)</span><span class="sxs-lookup"><span data-stu-id="9d875-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9d875-179">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9d875-179">Host configuration</span></span>

<span data-ttu-id="9d875-180">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="9d875-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9d875-181">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9d875-182">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9d875-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9d875-183">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9d875-184">`ConfigureHostConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9d875-185">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9d875-186">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9d875-187">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9d875-188">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9d875-189">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="9d875-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9d875-190">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9d875-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9d875-191">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9d875-191">App configuration</span></span>

<span data-ttu-id="9d875-192">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9d875-193">`ConfigureAppConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9d875-194">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9d875-195">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9d875-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9d875-196">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9d875-197">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9d875-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9d875-198">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="9d875-198">Settings for all app types</span></span>

<span data-ttu-id="9d875-199">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="9d875-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9d875-200">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="9d875-201">Daha fazla bilgi için [Varsayılan Oluşturucu ayarları](#default-builder-settings) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9d875-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9d875-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9d875-202">ApplicationName</span></span>

<span data-ttu-id="9d875-203">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9d875-204">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9d875-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9d875-205">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-205">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-206">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="9d875-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9d875-207">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9d875-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9d875-208">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9d875-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="9d875-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-209">ContentRoot</span></span>

<span data-ttu-id="9d875-210">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="9d875-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9d875-211">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="9d875-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9d875-212">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9d875-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9d875-213">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-213">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-214">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="9d875-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9d875-215">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9d875-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9d875-216">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9d875-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9d875-217">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="9d875-217">For more information, see:</span></span>

* [<span data-ttu-id="9d875-218">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="9d875-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9d875-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9d875-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9d875-220">EnvironmentName</span></span>

<span data-ttu-id="9d875-221">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9d875-222">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="9d875-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9d875-223">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9d875-224">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9d875-224">**Key**: `environment`</span></span>  
<span data-ttu-id="9d875-225">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-225">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-226">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9d875-226">**Default**: `Production`</span></span>  
<span data-ttu-id="9d875-227">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9d875-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9d875-228">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9d875-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9d875-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9d875-229">ShutdownTimeout</span></span>

<span data-ttu-id="9d875-230">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9d875-231">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-231">The default value is five seconds.</span></span>  <span data-ttu-id="9d875-232">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="9d875-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="9d875-233">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="9d875-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9d875-234">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9d875-235">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="9d875-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9d875-236">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="9d875-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9d875-237">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="9d875-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9d875-238">**Anahtar**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9d875-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9d875-239">**Şunu yazın**: `int`</span><span class="sxs-lookup"><span data-stu-id="9d875-239">**Type**: `int`</span></span>  
<span data-ttu-id="9d875-240">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="9d875-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9d875-241">**Ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9d875-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9d875-242">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="9d875-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9d875-243">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="9d875-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="9d875-244">Değişiklik sırasında uygulama yapılandırması yeniden yüklemeyi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="9d875-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="9d875-245">[Varsayılan](xref:fundamentals/configuration/index#default)olarak *appsettings.json* ve *appSettings. { Ortam}. JSON* , dosya değiştiğinde yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="9d875-246">ASP.NET Core 5,0 veya sonraki bir sürümde yeniden yükleme davranışını devre dışı bırakmak için `hostBuilder:reloadConfigOnChange` anahtarı olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="9d875-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="9d875-247">**Anahtar**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9d875-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9d875-248">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-249">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="9d875-249">**Default**: `true`</span></span>  
<span data-ttu-id="9d875-250">**Komut satırı bağımsız değişkeni**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9d875-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9d875-251">**Ortam değişkeni**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9d875-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="9d875-252">İki nokta ( `:` ) ayırıcısı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="9d875-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="9d875-253">Daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9d875-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="9d875-254">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="9d875-254">Settings for web apps</span></span>

<span data-ttu-id="9d875-255">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9d875-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9d875-256">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9d875-257">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9d875-258">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="9d875-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9d875-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9d875-259">CaptureStartupErrors</span></span>

<span data-ttu-id="9d875-260">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="9d875-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9d875-261">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9d875-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9d875-262">**Anahtar**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9d875-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9d875-263">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-264">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="9d875-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9d875-265">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9d875-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9d875-266">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="9d875-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9d875-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9d875-267">DetailedErrors</span></span>

<span data-ttu-id="9d875-268">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="9d875-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9d875-269">**Anahtar**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9d875-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9d875-270">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-271">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9d875-271">**Default**: `false`</span></span>  
<span data-ttu-id="9d875-272">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9d875-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9d875-273">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9d875-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9d875-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="9d875-275">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9d875-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9d875-276">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="9d875-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9d875-277">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9d875-278">**Anahtar**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9d875-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9d875-279">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-279">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-280">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9d875-280">**Default**: Empty string</span></span>  
<span data-ttu-id="9d875-281">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9d875-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9d875-282">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9d875-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9d875-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9d875-284">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9d875-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9d875-285">**Anahtar**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9d875-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9d875-286">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-286">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-287">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9d875-287">**Default**: Empty string</span></span>  
<span data-ttu-id="9d875-288">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9d875-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9d875-289">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9d875-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9d875-290">HTTPS_Port</span></span>

<span data-ttu-id="9d875-291">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="9d875-291">The HTTPS redirect port.</span></span> <span data-ttu-id="9d875-292">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9d875-293">**Anahtar**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9d875-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="9d875-294">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-294">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-295">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9d875-296">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9d875-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9d875-297">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9d875-298">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="9d875-298">PreferHostingUrls</span></span>

<span data-ttu-id="9d875-299">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="9d875-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9d875-300">**Anahtar**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9d875-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9d875-301">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-302">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="9d875-302">**Default**: `true`</span></span>  
<span data-ttu-id="9d875-303">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9d875-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9d875-304">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="9d875-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9d875-305">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="9d875-305">PreventHostingStartup</span></span>

<span data-ttu-id="9d875-306">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="9d875-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9d875-307">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9d875-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9d875-308">**Anahtar**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9d875-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9d875-309">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-310">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9d875-310">**Default**: `false`</span></span>  
<span data-ttu-id="9d875-311">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9d875-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9d875-312">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9d875-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9d875-313">StartupAssembly</span></span>

<span data-ttu-id="9d875-314">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9d875-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9d875-315">**Anahtar**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9d875-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9d875-316">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-316">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-317">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="9d875-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9d875-318">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9d875-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9d875-319">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="9d875-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9d875-320">`UseStartup` bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="9d875-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9d875-321">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="9d875-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9d875-322">URL’ler</span><span class="sxs-lookup"><span data-stu-id="9d875-322">URLs</span></span>

<span data-ttu-id="9d875-323">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9d875-324">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9d875-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9d875-325">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="9d875-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9d875-326">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9d875-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9d875-327">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="9d875-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="9d875-328">**Anahtar**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9d875-328">**Key**: `urls`</span></span>  
<span data-ttu-id="9d875-329">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-329">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-330">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9d875-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9d875-331">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9d875-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9d875-332">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="9d875-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9d875-333">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9d875-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9d875-334">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9d875-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9d875-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-335">WebRoot</span></span>

<span data-ttu-id="9d875-336">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="9d875-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="9d875-337">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="9d875-338">**Anahtar**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9d875-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="9d875-339">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-339">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-340">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="9d875-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9d875-341">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9d875-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="9d875-342">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9d875-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9d875-343">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9d875-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9d875-344">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="9d875-344">For more information, see:</span></span>

* [<span data-ttu-id="9d875-345">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="9d875-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9d875-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9d875-347">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="9d875-347">Manage the host lifetime</span></span>

<span data-ttu-id="9d875-348"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="9d875-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9d875-349">Bu yöntemler  <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="9d875-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9d875-350">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="9d875-350">Run</span></span>

<span data-ttu-id="9d875-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9d875-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9d875-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-352">RunAsync</span></span>

<span data-ttu-id="9d875-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9d875-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9d875-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-354">RunConsoleAsync</span></span>

<span data-ttu-id="9d875-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9d875-356">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="9d875-356">Start</span></span>

<span data-ttu-id="9d875-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="9d875-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9d875-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-358">StartAsync</span></span>

<span data-ttu-id="9d875-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9d875-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9d875-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9d875-361">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9d875-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-362">StopAsync</span></span>

<span data-ttu-id="9d875-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9d875-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9d875-364">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="9d875-364">WaitForShutdown</span></span>

<span data-ttu-id="9d875-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9d875-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9d875-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="9d875-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9d875-368">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="9d875-368">External control</span></span>

<span data-ttu-id="9d875-369">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="9d875-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9d875-370">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9d875-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="9d875-371">Bu konuda, ASP.NET Core .NET genel Host kullanımı hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="9d875-372">Konsol uygulamalarında .NET genel ana bilgisayarı kullanma hakkında bilgi için bkz. [.NET genel ana bilgisayar](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="9d875-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="9d875-373">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="9d875-373">Host definition</span></span>

<span data-ttu-id="9d875-374">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="9d875-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9d875-375">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="9d875-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="9d875-376">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="9d875-376">Logging</span></span>
* <span data-ttu-id="9d875-377">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9d875-377">Configuration</span></span>
* <span data-ttu-id="9d875-378">`IHostedService` uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="9d875-378">`IHostedService` implementations</span></span>

<span data-ttu-id="9d875-379">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="9d875-380">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="9d875-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9d875-381">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="9d875-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9d875-382">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="9d875-382">Set up a host</span></span>

<span data-ttu-id="9d875-383">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="9d875-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9d875-384">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d875-384">The `Main` method:</span></span>

* <span data-ttu-id="9d875-385">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9d875-386">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="9d875-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9d875-387">ASP.NET Core Web şablonları, genel bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9d875-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="9d875-388">Aşağıdaki kod, HTTP olmayan iş yükünü kullanan genel bir konak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9d875-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="9d875-389">`IHostedService`Uygulama, dı kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="9d875-389">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="9d875-390">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="9d875-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9d875-391">Yukarıdaki kod ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9d875-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="9d875-392">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9d875-393">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9d875-394">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="9d875-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9d875-395">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="9d875-395">Default builder settings</span></span>

<span data-ttu-id="9d875-396"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d875-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="9d875-397">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="9d875-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="9d875-398">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9d875-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="9d875-399">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9d875-400">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-400">Command-line arguments.</span></span>
* <span data-ttu-id="9d875-401">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="9d875-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="9d875-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9d875-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="9d875-403">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9d875-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9d875-404">Uygulama ortamda çalıştırıldığında [Kullanıcı gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="9d875-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9d875-405">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-405">Environment variables.</span></span>
  * <span data-ttu-id="9d875-406">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-406">Command-line arguments.</span></span>
* <span data-ttu-id="9d875-407">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="9d875-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9d875-408">Konsol</span><span class="sxs-lookup"><span data-stu-id="9d875-408">Console</span></span>
  * <span data-ttu-id="9d875-409">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9d875-409">Debug</span></span>
  * <span data-ttu-id="9d875-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="9d875-410">EventSource</span></span>
  * <span data-ttu-id="9d875-411">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="9d875-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9d875-412">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9d875-413">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d875-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9d875-414">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9d875-415">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9d875-416">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="9d875-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9d875-417">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9d875-418">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="9d875-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9d875-419">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-419">Enables IIS integration.</span></span> <span data-ttu-id="9d875-420">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="9d875-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9d875-421">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9d875-422">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="9d875-422">Framework-provided services</span></span>

<span data-ttu-id="9d875-423">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="9d875-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9d875-424">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9d875-425">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9d875-426">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="9d875-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9d875-427">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="9d875-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9d875-428">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="9d875-429"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9d875-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9d875-430">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="9d875-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9d875-431">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="9d875-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9d875-432">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="9d875-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9d875-433">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-433">IHostLifetime</span></span>

<span data-ttu-id="9d875-434"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="9d875-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9d875-435">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-435">The last implementation registered is used.</span></span>

<span data-ttu-id="9d875-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="9d875-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9d875-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9d875-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9d875-438"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="9d875-439">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9d875-440">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="9d875-440">IHostEnvironment</span></span>

<span data-ttu-id="9d875-441"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9d875-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9d875-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9d875-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9d875-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9d875-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9d875-444">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="9d875-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="9d875-445">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [](#webroot)</span><span class="sxs-lookup"><span data-stu-id="9d875-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9d875-446">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9d875-446">Host configuration</span></span>

<span data-ttu-id="9d875-447">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="9d875-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9d875-448">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="9d875-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="9d875-449">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9d875-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9d875-450">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="9d875-451">`ConfigureHostConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9d875-452">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9d875-453">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9d875-454">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9d875-455">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9d875-456">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="9d875-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9d875-457">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9d875-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9d875-458">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9d875-458">App configuration</span></span>

<span data-ttu-id="9d875-459">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9d875-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9d875-460">`ConfigureAppConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9d875-461">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9d875-462">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9d875-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9d875-463">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9d875-464">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9d875-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9d875-465">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="9d875-465">Settings for all app types</span></span>

<span data-ttu-id="9d875-466">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="9d875-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9d875-467">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9d875-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9d875-468">ApplicationName</span></span>

<span data-ttu-id="9d875-469">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9d875-470">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9d875-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9d875-471">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-471">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-472">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="9d875-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9d875-473">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9d875-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9d875-474">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9d875-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="9d875-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-475">ContentRoot</span></span>

<span data-ttu-id="9d875-476">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="9d875-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9d875-477">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="9d875-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9d875-478">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9d875-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9d875-479">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-479">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-480">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="9d875-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9d875-481">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9d875-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9d875-482">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9d875-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9d875-483">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="9d875-483">For more information, see:</span></span>

* [<span data-ttu-id="9d875-484">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="9d875-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9d875-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9d875-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9d875-486">EnvironmentName</span></span>

<span data-ttu-id="9d875-487">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9d875-488">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="9d875-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9d875-489">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9d875-490">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9d875-490">**Key**: `environment`</span></span>  
<span data-ttu-id="9d875-491">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-491">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-492">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9d875-492">**Default**: `Production`</span></span>  
<span data-ttu-id="9d875-493">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9d875-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9d875-494">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9d875-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9d875-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9d875-495">ShutdownTimeout</span></span>

<span data-ttu-id="9d875-496">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9d875-497">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-497">The default value is five seconds.</span></span>  <span data-ttu-id="9d875-498">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="9d875-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="9d875-499">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="9d875-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9d875-500">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9d875-501">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="9d875-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9d875-502">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="9d875-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9d875-503">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="9d875-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9d875-504">**Anahtar**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9d875-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9d875-505">**Şunu yazın**: `int`</span><span class="sxs-lookup"><span data-stu-id="9d875-505">**Type**: `int`</span></span>  
<span data-ttu-id="9d875-506">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="9d875-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9d875-507">**Ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9d875-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9d875-508">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="9d875-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9d875-509">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="9d875-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="9d875-510">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="9d875-510">Settings for web apps</span></span>

<span data-ttu-id="9d875-511">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9d875-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9d875-512">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9d875-513">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9d875-514">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="9d875-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9d875-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9d875-515">CaptureStartupErrors</span></span>

<span data-ttu-id="9d875-516">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="9d875-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9d875-517">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9d875-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9d875-518">**Anahtar**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9d875-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9d875-519">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-520">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="9d875-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9d875-521">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9d875-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9d875-522">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="9d875-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9d875-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9d875-523">DetailedErrors</span></span>

<span data-ttu-id="9d875-524">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="9d875-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9d875-525">**Anahtar**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9d875-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9d875-526">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-527">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9d875-527">**Default**: `false`</span></span>  
<span data-ttu-id="9d875-528">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9d875-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9d875-529">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9d875-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9d875-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="9d875-531">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9d875-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9d875-532">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="9d875-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9d875-533">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9d875-534">**Anahtar**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9d875-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9d875-535">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-535">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-536">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9d875-536">**Default**: Empty string</span></span>  
<span data-ttu-id="9d875-537">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9d875-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9d875-538">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9d875-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9d875-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9d875-540">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="9d875-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9d875-541">**Anahtar**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9d875-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9d875-542">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-542">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-543">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="9d875-543">**Default**: Empty string</span></span>  
<span data-ttu-id="9d875-544">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9d875-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9d875-545">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9d875-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9d875-546">HTTPS_Port</span></span>

<span data-ttu-id="9d875-547">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="9d875-547">The HTTPS redirect port.</span></span> <span data-ttu-id="9d875-548">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9d875-549">**Anahtar**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9d875-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="9d875-550">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-550">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-551">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9d875-552">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9d875-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9d875-553">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9d875-554">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="9d875-554">PreferHostingUrls</span></span>

<span data-ttu-id="9d875-555">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="9d875-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9d875-556">**Anahtar**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9d875-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9d875-557">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-558">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="9d875-558">**Default**: `true`</span></span>  
<span data-ttu-id="9d875-559">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9d875-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9d875-560">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="9d875-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9d875-561">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="9d875-561">PreventHostingStartup</span></span>

<span data-ttu-id="9d875-562">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="9d875-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9d875-563">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9d875-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9d875-564">**Anahtar**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9d875-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9d875-565">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="9d875-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9d875-566">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="9d875-566">**Default**: `false`</span></span>  
<span data-ttu-id="9d875-567">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9d875-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9d875-568">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="9d875-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9d875-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9d875-569">StartupAssembly</span></span>

<span data-ttu-id="9d875-570">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9d875-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9d875-571">**Anahtar**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9d875-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9d875-572">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-572">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-573">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="9d875-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9d875-574">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9d875-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9d875-575">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="9d875-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9d875-576">`UseStartup` bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="9d875-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9d875-577">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="9d875-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9d875-578">URL’ler</span><span class="sxs-lookup"><span data-stu-id="9d875-578">URLs</span></span>

<span data-ttu-id="9d875-579">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9d875-580">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9d875-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9d875-581">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="9d875-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9d875-582">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9d875-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9d875-583">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="9d875-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="9d875-584">**Anahtar**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9d875-584">**Key**: `urls`</span></span>  
<span data-ttu-id="9d875-585">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-585">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-586">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9d875-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9d875-587">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9d875-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9d875-588">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="9d875-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9d875-589">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9d875-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9d875-590">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9d875-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9d875-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-591">WebRoot</span></span>

<span data-ttu-id="9d875-592">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="9d875-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="9d875-593">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="9d875-594">**Anahtar**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9d875-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="9d875-595">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-595">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-596">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="9d875-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9d875-597">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9d875-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="9d875-598">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9d875-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9d875-599">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9d875-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9d875-600">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="9d875-600">For more information, see:</span></span>

* [<span data-ttu-id="9d875-601">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="9d875-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9d875-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="9d875-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9d875-603">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="9d875-603">Manage the host lifetime</span></span>

<span data-ttu-id="9d875-604"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="9d875-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9d875-605">Bu yöntemler  <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="9d875-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9d875-606">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="9d875-606">Run</span></span>

<span data-ttu-id="9d875-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9d875-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9d875-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-608">RunAsync</span></span>

<span data-ttu-id="9d875-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9d875-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9d875-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-610">RunConsoleAsync</span></span>

<span data-ttu-id="9d875-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9d875-612">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="9d875-612">Start</span></span>

<span data-ttu-id="9d875-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="9d875-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9d875-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-614">StartAsync</span></span>

<span data-ttu-id="9d875-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9d875-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9d875-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9d875-617">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9d875-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-618">StopAsync</span></span>

<span data-ttu-id="9d875-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9d875-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9d875-620">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="9d875-620">WaitForShutdown</span></span>

<span data-ttu-id="9d875-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="9d875-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9d875-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="9d875-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9d875-624">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="9d875-624">External control</span></span>

<span data-ttu-id="9d875-625">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="9d875-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9d875-626">ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="9d875-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="9d875-627">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="9d875-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="9d875-628">Bu makalede <xref:Microsoft.Extensions.Hosting.HostBuilder> , http isteklerini işlemeyecek uygulamalar için kullanılan genel ana bilgisayar () ASP.NET Core ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9d875-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="9d875-629">Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="9d875-630">Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.</span><span class="sxs-lookup"><span data-stu-id="9d875-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="9d875-631">Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="9d875-632">Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="9d875-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9d875-633">Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.</span><span class="sxs-lookup"><span data-stu-id="9d875-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="9d875-634">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9d875-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9d875-635">Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal* kullanın.</span><span class="sxs-lookup"><span data-stu-id="9d875-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="9d875-636">Örneği bir içinde çalıştırmayın `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="9d875-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="9d875-637">Konsolu Visual Studio Code ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="9d875-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="9d875-638">Dosyasında *. vscode/launch.js* açın.</span><span class="sxs-lookup"><span data-stu-id="9d875-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="9d875-639">**.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="9d875-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="9d875-640">Değeri ya da olarak ayarlayın `externalTerminal` `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="9d875-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="9d875-641">Giriş</span><span class="sxs-lookup"><span data-stu-id="9d875-641">Introduction</span></span>

<span data-ttu-id="9d875-642">Genel konak kitaplığı ad alanında kullanılabilir <xref:Microsoft.Extensions.Hosting> ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="9d875-643">`Microsoft.Extensions.Hosting`Paket, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="9d875-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="9d875-644"><xref:Microsoft.Extensions.Hosting.IHostedService> , kod yürütmeye yönelik giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="9d875-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="9d875-645">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices içinde hizmet kaydı](#configureservices)sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="9d875-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="9d875-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> , <xref:Microsoft.Extensions.Hosting.IHostedService> konak başlatıldığında çağrılır ve <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ana bilgisayar düzgün bir şekilde kapandığında ters kayıt sırasında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9d875-647">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="9d875-647">Set up a host</span></span>

<span data-ttu-id="9d875-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> , kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="9d875-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="9d875-649">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="9d875-649">Options</span></span>

<span data-ttu-id="9d875-650"><xref:Microsoft.Extensions.Hosting.HostOptions> için seçenekleri yapılandırın <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="9d875-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="9d875-651">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="9d875-651">Shutdown timeout</span></span>

<span data-ttu-id="9d875-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9d875-653">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="9d875-653">The default value is five seconds.</span></span>

<span data-ttu-id="9d875-654">İçindeki aşağıdaki seçenek yapılandırması, `Program.Main` varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:</span><span class="sxs-lookup"><span data-stu-id="9d875-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="9d875-655">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="9d875-655">Default services</span></span>

<span data-ttu-id="9d875-656">Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="9d875-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="9d875-657">[Ortam](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="9d875-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="9d875-658">[Yapılandırma](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="9d875-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="9d875-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9d875-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="9d875-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9d875-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="9d875-661">[Seçenekler](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="9d875-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="9d875-662">[Günlüğe kaydetme](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="9d875-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9d875-663">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9d875-663">Host configuration</span></span>

<span data-ttu-id="9d875-664">Ana bilgisayar yapılandırması şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9d875-664">Host configuration is created by:</span></span>

* <span data-ttu-id="9d875-665"><xref:Microsoft.Extensions.Hosting.IHostBuilder> [İçerik kökünü](#content-root) ve [ortamı](#environment)ayarlamak için uzantı yöntemleri çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="9d875-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="9d875-666">İçindeki yapılandırma sağlayıcılarından yapılandırma okunuyor <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="9d875-667">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="9d875-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="9d875-668">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="9d875-668">Application key (name)</span></span>

<span data-ttu-id="9d875-669">[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="9d875-670">Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="9d875-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="9d875-671">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9d875-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9d875-672">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-672">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-673">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="9d875-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="9d875-674">Şunu **kullanarak ayarla**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="9d875-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="9d875-675">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9d875-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="9d875-676">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="9d875-676">Content root</span></span>

<span data-ttu-id="9d875-677">Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="9d875-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="9d875-678">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9d875-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9d875-679">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-679">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-680">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="9d875-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="9d875-681">Şunu **kullanarak ayarla**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="9d875-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="9d875-682">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9d875-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9d875-683">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="9d875-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="9d875-684">Daha fazla bilgi için bkz. [temel bilgiler: içerik kökü](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="9d875-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="9d875-685">Ortam</span><span class="sxs-lookup"><span data-stu-id="9d875-685">Environment</span></span>

<span data-ttu-id="9d875-686">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9d875-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9d875-687">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9d875-687">**Key**: `environment`</span></span>  
<span data-ttu-id="9d875-688">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="9d875-688">**Type**: `string`</span></span>  
<span data-ttu-id="9d875-689">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9d875-689">**Default**: `Production`</span></span>  
<span data-ttu-id="9d875-690">Şunu **kullanarak ayarla**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="9d875-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="9d875-691">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9d875-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9d875-692">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-692">The environment can be set to any value.</span></span> <span data-ttu-id="9d875-693">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="9d875-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9d875-694">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="9d875-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="9d875-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="9d875-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>konak için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="9d875-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="9d875-697">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın derleme sürecinde kullanılmak üzere başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="9d875-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9d875-699">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9d875-700">Varsayılan olarak hiçbir sağlayıcı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="9d875-700">No providers are included by default.</span></span> <span data-ttu-id="9d875-701">Uygulamanın gerektirdiği her türlü yapılandırma sağlayıcısını açık bir şekilde belirtmeniz gerekir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , örneğin:</span><span class="sxs-lookup"><span data-stu-id="9d875-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="9d875-702">Dosya yapılandırması (örneğin, dosyadaki bir *hostsettings.js* ).</span><span class="sxs-lookup"><span data-stu-id="9d875-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="9d875-703">Ortam değişkeni yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="9d875-703">Environment variable configuration.</span></span>
* <span data-ttu-id="9d875-704">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="9d875-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="9d875-705">Diğer tüm gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="9d875-705">Any other required configuration providers.</span></span>

<span data-ttu-id="9d875-706">Konağın dosya yapılandırması, uygulamanın temel yolu ve `SetBasePath` ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="9d875-707">Örnek uygulama, *üzerindehostsettings.js* bir JSON dosyası ve <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> dosyanın ana bilgisayar yapılandırma ayarlarını kullanmak için çağrılar kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="9d875-708">Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak Oluşturucu ' ya çağrı yapın <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="9d875-709">`AddEnvironmentVariables` Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d875-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="9d875-710">Örnek uygulama, öneki kullanır `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="9d875-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="9d875-711">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9d875-712">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, için ortam değişkeni değeri `PREFIX_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="9d875-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9d875-713">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya ile bir uygulama çalıştırırken geliştirme sırasında `dotnet run` , ortam değişkenleri dosyadaki *Özellikler/launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="9d875-714">[Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında dosya *üzerinde. vscode/launch.js* içinde ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="9d875-715">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9d875-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9d875-716">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağırarak eklenir <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="9d875-717">Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="9d875-718">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="9d875-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="9d875-719">Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="9d875-720">`HostBuilder`Kullanarak örnek yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="9d875-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="9d875-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9d875-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9d875-722">Uygulama yapılandırması, uygulama çağrısı yaparak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9d875-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="9d875-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>uygulama için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="9d875-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="9d875-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9d875-725">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d875-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="9d875-726">Tarafından oluşturulan yapılandırma, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sonraki işlemler ve içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bölümünde mevcuttur <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="9d875-727">Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9d875-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="9d875-728">Kullanarak örnek uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="9d875-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="9d875-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9d875-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="9d875-730">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="9d875-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="9d875-731">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="9d875-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="9d875-732">Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d875-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="9d875-733">Örnek uygulama, JSON uygulama ayarları dosyalarını ve *hostsettings.js* aşağıdaki öğeyle taşıdıkça `<Content>` :</span><span class="sxs-lookup"><span data-stu-id="9d875-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="9d875-734">Ve gibi yapılandırma uzantısı yöntemleri, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.Js](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) veMicrosoft.Extensions.Configuration gibi ek NuGet paketleri gerektirir [ . EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="9d875-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="9d875-735">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadıkça, bu paketlerin çekirdek [Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9d875-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="9d875-736">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9d875-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="9d875-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="9d875-737">ConfigureServices</span></span>

<span data-ttu-id="9d875-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9d875-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9d875-740">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="9d875-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="9d875-741">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="9d875-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="9d875-742">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `AddHostedService` yaşam süresi olayları, `LifetimeEventsHostedService` ve zamanlanan bir arka plan görevi için uygulamaya bir hizmet eklemek için genişletme yöntemini kullanır `TimedHostedService` :</span><span class="sxs-lookup"><span data-stu-id="9d875-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="9d875-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="9d875-743">ConfigureLogging</span></span>

<span data-ttu-id="9d875-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> belirtilen yapılandırmayı yapılandırmak için bir temsilci ekler <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9d875-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="9d875-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="9d875-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="9d875-746">UseConsoleLifetime</span></span>

<span data-ttu-id="9d875-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="9d875-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="9d875-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan yaşam süresi uygulamasına önceden kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="9d875-750">Kaydedilen son yaşam süresi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d875-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="9d875-751">Kapsayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9d875-751">Container configuration</span></span>

<span data-ttu-id="9d875-752">Diğer kapsayıcıları takmayı desteklemek için ana bilgisayar bir kabul edebilir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="9d875-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="9d875-753">Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır.</span><span class="sxs-lookup"><span data-stu-id="9d875-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="9d875-754">[Useserviceproviderfactory (ıvıceproviderfactory &lt; tcontainerbuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="9d875-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="9d875-755">Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemiyle yönetilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="9d875-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d875-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="9d875-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9d875-758">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="9d875-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="9d875-759">Hizmet kapsayıcısı fabrikası sağlama:</span><span class="sxs-lookup"><span data-stu-id="9d875-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="9d875-760">Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="9d875-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="9d875-761">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="9d875-761">Extensibility</span></span>

<span data-ttu-id="9d875-762">Konak genişletilebilirliği, üzerindeki genişletme yöntemleriyle gerçekleştirilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9d875-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="9d875-763">Aşağıdaki örnek, bir genişletme yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> ' de gösterilen [Timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir uygulamayı nasıl genişlettiğini gösterir <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="9d875-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="9d875-764">Uygulama, `UseHostedService` geçirilen barındırılan hizmeti kaydetmek için uzantı yöntemi oluşturur `T` :</span><span class="sxs-lookup"><span data-stu-id="9d875-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="9d875-765">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="9d875-765">Manage the host</span></span>

<span data-ttu-id="9d875-766"><xref:Microsoft.Extensions.Hosting.IHost>Uygulama, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı olan uygulamaları başlatıp durdurmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="9d875-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9d875-767">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="9d875-767">Run</span></span>

<span data-ttu-id="9d875-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="9d875-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="9d875-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-769">RunAsync</span></span>

<span data-ttu-id="9d875-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="9d875-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="9d875-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-771">RunConsoleAsync</span></span>

<span data-ttu-id="9d875-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="9d875-773">Başlangıç ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-773">Start and StopAsync</span></span>

<span data-ttu-id="9d875-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="9d875-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="9d875-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9d875-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="9d875-776">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="9d875-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="9d875-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="9d875-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> uygulamayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="9d875-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="9d875-779">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="9d875-779">WaitForShutdown</span></span>

<span data-ttu-id="9d875-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, (örneğin, <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterim dinler) ile tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="9d875-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="9d875-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> çağırır <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="9d875-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9d875-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="9d875-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9d875-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="9d875-784">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="9d875-784">External control</span></span>

<span data-ttu-id="9d875-785">Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:</span><span class="sxs-lookup"><span data-stu-id="9d875-785">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9d875-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="9d875-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9d875-787">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="9d875-788">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="9d875-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="9d875-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d875-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="9d875-790">Özelliklerini ve uzantı yöntemlerini kullanmak üzere sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> :</span><span class="sxs-lookup"><span data-stu-id="9d875-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="9d875-791">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9d875-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="9d875-792">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="9d875-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="9d875-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> düzgün kapanma istekleri de dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="9d875-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="9d875-794">Arabirimdeki üç özellik, <xref:System.Action> Başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="9d875-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="9d875-795">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="9d875-795">Cancellation Token</span></span> | <span data-ttu-id="9d875-796">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="9d875-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="9d875-797">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="9d875-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="9d875-798">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="9d875-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="9d875-799">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9d875-799">All requests should be processed.</span></span> <span data-ttu-id="9d875-800">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="9d875-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="9d875-801">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="9d875-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="9d875-802">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="9d875-802">Requests may still be processing.</span></span> <span data-ttu-id="9d875-803">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="9d875-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="9d875-804">Constructor- <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmeti herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9d875-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="9d875-805">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `LifetimeEventsHostedService` olayları kaydetmek için bir sınıfa (bir uygulama) ekleme oluşturucusunu kullanır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="9d875-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="9d875-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d875-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="9d875-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="9d875-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="9d875-808">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır `Shutdown` :</span><span class="sxs-lookup"><span data-stu-id="9d875-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9d875-809">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9d875-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
