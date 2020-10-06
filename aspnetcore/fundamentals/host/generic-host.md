---
title: ASP.NET Core .NET genel ana bilgisayarı
author: rick-anderson
description: ASP.NET Core uygulamalarında .NET Core genel ana bilgisayarı kullanın.  Genel ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.
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
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762327"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="0d725-104">ASP.NET Core .NET genel ana bilgisayarı</span><span class="sxs-lookup"><span data-stu-id="0d725-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0d725-105">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0d725-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="0d725-106">Bu konuda, ASP.NET Core .NET genel Host kullanımı hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="0d725-107">Konsol uygulamalarında .NET genel ana bilgisayarı kullanma hakkında bilgi için bkz. [.NET genel ana bilgisayar](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="0d725-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0d725-108">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="0d725-108">Host definition</span></span>

<span data-ttu-id="0d725-109">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0d725-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0d725-110">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="0d725-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="0d725-111">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="0d725-111">Logging</span></span>
* <span data-ttu-id="0d725-112">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0d725-112">Configuration</span></span>
* <span data-ttu-id="0d725-113">`IHostedService` uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="0d725-113">`IHostedService` implementations</span></span>

<span data-ttu-id="0d725-114">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="0d725-115">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="0d725-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0d725-116">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="0d725-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0d725-117">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="0d725-117">Set up a host</span></span>

<span data-ttu-id="0d725-118">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="0d725-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0d725-119">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0d725-119">The `Main` method:</span></span>

* <span data-ttu-id="0d725-120">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0d725-121">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="0d725-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0d725-122">ASP.NET Core Web şablonları, bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0d725-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="0d725-123">Aşağıdaki kod, dı kapsayıcısına eklenen bir uygulamayla HTTP olmayan bir iş yükü oluşturur `IHostedService` .</span><span class="sxs-lookup"><span data-stu-id="0d725-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="0d725-124">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="0d725-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0d725-125">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0d725-126">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0d725-127">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0d725-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0d725-128">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="0d725-128">Default builder settings</span></span>

<span data-ttu-id="0d725-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0d725-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="0d725-130">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="0d725-131">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0d725-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="0d725-132">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0d725-133">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-133">Command-line arguments.</span></span>
* <span data-ttu-id="0d725-134">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0d725-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="0d725-135">\* Üzerindeappsettings.js\*.</span><span class="sxs-lookup"><span data-stu-id="0d725-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="0d725-136">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0d725-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0d725-137">Uygulama ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) `Development` .</span><span class="sxs-lookup"><span data-stu-id="0d725-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0d725-138">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-138">Environment variables.</span></span>
  * <span data-ttu-id="0d725-139">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-139">Command-line arguments.</span></span>
* <span data-ttu-id="0d725-140">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="0d725-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0d725-141">Konsol</span><span class="sxs-lookup"><span data-stu-id="0d725-141">Console</span></span>
  * <span data-ttu-id="0d725-142">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="0d725-142">Debug</span></span>
  * <span data-ttu-id="0d725-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d725-143">EventSource</span></span>
  * <span data-ttu-id="0d725-144">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="0d725-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0d725-145">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0d725-146">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0d725-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0d725-147">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0d725-148">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0d725-149">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="0d725-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0d725-150">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0d725-151">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="0d725-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0d725-152">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-152">Enables IIS integration.</span></span> <span data-ttu-id="0d725-153">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="0d725-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0d725-154">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0d725-155">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0d725-155">Framework-provided services</span></span>

<span data-ttu-id="0d725-156">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0d725-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0d725-157">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0d725-158">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0d725-159">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="0d725-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0d725-160">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="0d725-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0d725-161">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="0d725-162"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d725-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0d725-163">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="0d725-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0d725-164">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="0d725-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0d725-165">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="0d725-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0d725-166">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-166">IHostLifetime</span></span>

<span data-ttu-id="0d725-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="0d725-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0d725-168">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-168">The last implementation registered is used.</span></span>

<span data-ttu-id="0d725-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="0d725-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0d725-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0d725-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0d725-171"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="0d725-172">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0d725-173">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="0d725-173">IHostEnvironment</span></span>

<span data-ttu-id="0d725-174"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0d725-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0d725-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0d725-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0d725-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0d725-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0d725-177">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="0d725-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0d725-178">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [WebRootPath](#webroot)</span><span class="sxs-lookup"><span data-stu-id="0d725-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0d725-179">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0d725-179">Host configuration</span></span>

<span data-ttu-id="0d725-180">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="0d725-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0d725-181">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="0d725-182">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0d725-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0d725-183">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0d725-184">`ConfigureHostConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0d725-185">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0d725-186">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0d725-187">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0d725-188">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0d725-189">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="0d725-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0d725-190">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0d725-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0d725-191">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0d725-191">App configuration</span></span>

<span data-ttu-id="0d725-192">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0d725-193">`ConfigureAppConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0d725-194">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0d725-195">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0d725-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0d725-196">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0d725-197">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="0d725-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0d725-198">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="0d725-198">Settings for all app types</span></span>

<span data-ttu-id="0d725-199">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="0d725-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0d725-200">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0d725-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0d725-201">ApplicationName</span></span>

<span data-ttu-id="0d725-202">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0d725-203">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="0d725-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0d725-204">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-204">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-205">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="0d725-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0d725-206">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0d725-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0d725-207">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d725-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0d725-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-208">ContentRoot</span></span>

<span data-ttu-id="0d725-209">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="0d725-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0d725-210">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="0d725-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0d725-211">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0d725-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0d725-212">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-212">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-213">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="0d725-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0d725-214">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0d725-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0d725-215">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0d725-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0d725-216">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0d725-216">For more information, see:</span></span>

* [<span data-ttu-id="0d725-217">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="0d725-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0d725-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0d725-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0d725-219">EnvironmentName</span></span>

<span data-ttu-id="0d725-220">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0d725-221">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="0d725-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0d725-222">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0d725-223">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="0d725-223">**Key**: `environment`</span></span>  
<span data-ttu-id="0d725-224">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-224">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-225">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="0d725-225">**Default**: `Production`</span></span>  
<span data-ttu-id="0d725-226">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0d725-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0d725-227">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0d725-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0d725-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0d725-228">ShutdownTimeout</span></span>

<span data-ttu-id="0d725-229">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0d725-230">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-230">The default value is five seconds.</span></span>  <span data-ttu-id="0d725-231">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="0d725-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="0d725-232">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="0d725-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0d725-233">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0d725-234">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="0d725-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0d725-235">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="0d725-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0d725-236">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="0d725-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0d725-237">**Anahtar**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0d725-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0d725-238">**Şunu yazın**: `int`</span><span class="sxs-lookup"><span data-stu-id="0d725-238">**Type**: `int`</span></span>  
<span data-ttu-id="0d725-239">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="0d725-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0d725-240">**Ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0d725-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0d725-241">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="0d725-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0d725-242">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0d725-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="0d725-243">Değişiklik sırasında uygulama yapılandırması yeniden yüklemeyi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0d725-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="0d725-244">[Varsayılan](xref:fundamentals/configuration/index#default)olarak, *appsettings.json* ve appSettingsappsettings.js *. { Ortam}. JSON* , dosya değiştiğinde yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="0d725-245">ASP.NET Core 5,0 veya sonraki bir sürümde yeniden yükleme davranışını devre dışı bırakmak için `hostBuilder:reloadConfigOnChange` anahtarı olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="0d725-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="0d725-246">**Anahtar**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0d725-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0d725-247">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-248">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="0d725-248">**Default**: `true`</span></span>  
<span data-ttu-id="0d725-249">**Komut satırı bağımsız değişkeni**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0d725-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0d725-250">**Ortam değişkeni**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0d725-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="0d725-251">İki nokta ( `:` ) ayırıcısı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="0d725-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="0d725-252">Daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="0d725-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="0d725-253">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="0d725-253">Settings for web apps</span></span>

<span data-ttu-id="0d725-254">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0d725-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0d725-255">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0d725-256">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0d725-257">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="0d725-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0d725-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0d725-258">CaptureStartupErrors</span></span>

<span data-ttu-id="0d725-259">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="0d725-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0d725-260">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0d725-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0d725-261">**Anahtar**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0d725-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0d725-262">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-263">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="0d725-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0d725-264">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0d725-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0d725-265">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="0d725-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0d725-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0d725-266">DetailedErrors</span></span>

<span data-ttu-id="0d725-267">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="0d725-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0d725-268">**Anahtar**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0d725-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0d725-269">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-270">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="0d725-270">**Default**: `false`</span></span>  
<span data-ttu-id="0d725-271">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0d725-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0d725-272">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0d725-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0d725-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="0d725-274">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0d725-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0d725-275">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="0d725-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0d725-276">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0d725-277">**Anahtar**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0d725-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0d725-278">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-278">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-279">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0d725-279">**Default**: Empty string</span></span>  
<span data-ttu-id="0d725-280">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0d725-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0d725-281">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0d725-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0d725-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0d725-283">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0d725-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0d725-284">**Anahtar**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0d725-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0d725-285">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-285">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-286">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0d725-286">**Default**: Empty string</span></span>  
<span data-ttu-id="0d725-287">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0d725-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0d725-288">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0d725-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0d725-289">HTTPS_Port</span></span>

<span data-ttu-id="0d725-290">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="0d725-290">The HTTPS redirect port.</span></span> <span data-ttu-id="0d725-291">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0d725-292">**Anahtar**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="0d725-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="0d725-293">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-293">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-294">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0d725-295">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0d725-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0d725-296">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0d725-297">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="0d725-297">PreferHostingUrls</span></span>

<span data-ttu-id="0d725-298">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="0d725-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0d725-299">**Anahtar**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0d725-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0d725-300">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-301">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="0d725-301">**Default**: `true`</span></span>  
<span data-ttu-id="0d725-302">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0d725-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0d725-303">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="0d725-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0d725-304">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="0d725-304">PreventHostingStartup</span></span>

<span data-ttu-id="0d725-305">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="0d725-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0d725-306">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0d725-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0d725-307">**Anahtar**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0d725-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0d725-308">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-309">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="0d725-309">**Default**: `false`</span></span>  
<span data-ttu-id="0d725-310">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0d725-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0d725-311">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0d725-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0d725-312">StartupAssembly</span></span>

<span data-ttu-id="0d725-313">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0d725-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0d725-314">**Anahtar**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0d725-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0d725-315">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-315">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-316">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="0d725-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0d725-317">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0d725-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0d725-318">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="0d725-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0d725-319">`UseStartup` bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="0d725-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0d725-320">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="0d725-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0d725-321">URL’ler</span><span class="sxs-lookup"><span data-stu-id="0d725-321">URLs</span></span>

<span data-ttu-id="0d725-322">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0d725-323">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0d725-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0d725-324">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="0d725-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0d725-325">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d725-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0d725-326">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="0d725-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="0d725-327">**Anahtar**: `urls`</span><span class="sxs-lookup"><span data-stu-id="0d725-327">**Key**: `urls`</span></span>  
<span data-ttu-id="0d725-328">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-328">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-329">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0d725-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0d725-330">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0d725-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0d725-331">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="0d725-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0d725-332">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0d725-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0d725-333">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0d725-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0d725-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-334">WebRoot</span></span>

<span data-ttu-id="0d725-335">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="0d725-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0d725-336">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0d725-337">**Anahtar**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="0d725-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="0d725-338">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-338">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-339">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0d725-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0d725-340">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d725-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0d725-341">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0d725-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0d725-342">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0d725-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0d725-343">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0d725-343">For more information, see:</span></span>

* [<span data-ttu-id="0d725-344">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="0d725-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0d725-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0d725-346">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="0d725-346">Manage the host lifetime</span></span>

<span data-ttu-id="0d725-347"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="0d725-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0d725-348">Bu yöntemler  <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="0d725-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0d725-349">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="0d725-349">Run</span></span>

<span data-ttu-id="0d725-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0d725-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0d725-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-351">RunAsync</span></span>

<span data-ttu-id="0d725-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0d725-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0d725-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-353">RunConsoleAsync</span></span>

<span data-ttu-id="0d725-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0d725-355">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="0d725-355">Start</span></span>

<span data-ttu-id="0d725-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="0d725-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0d725-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-357">StartAsync</span></span>

<span data-ttu-id="0d725-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0d725-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0d725-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0d725-360">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0d725-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-361">StopAsync</span></span>

<span data-ttu-id="0d725-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0d725-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0d725-363">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="0d725-363">WaitForShutdown</span></span>

<span data-ttu-id="0d725-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0d725-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0d725-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="0d725-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0d725-367">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="0d725-367">External control</span></span>

<span data-ttu-id="0d725-368">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="0d725-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0d725-369">ASP.NET Core şablonları bir .NET Core genel Konağı () oluşturur <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0d725-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="0d725-370">Bu konuda, ASP.NET Core .NET genel Host kullanımı hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="0d725-371">Konsol uygulamalarında .NET genel ana bilgisayarı kullanma hakkında bilgi için bkz. [.NET genel ana bilgisayar](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="0d725-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0d725-372">Ana bilgisayar tanımı</span><span class="sxs-lookup"><span data-stu-id="0d725-372">Host definition</span></span>

<span data-ttu-id="0d725-373">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0d725-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0d725-374">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="0d725-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="0d725-375">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="0d725-375">Logging</span></span>
* <span data-ttu-id="0d725-376">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0d725-376">Configuration</span></span>
* <span data-ttu-id="0d725-377">`IHostedService` uygulamalarını</span><span class="sxs-lookup"><span data-stu-id="0d725-377">`IHostedService` implementations</span></span>

<span data-ttu-id="0d725-378">Bir konak başlatıldığında, <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısının barındırılan hizmetler koleksiyonunda kayıtlı her bir uygulamasına çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="0d725-379">Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="0d725-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0d725-380">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="0d725-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0d725-381">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="0d725-381">Set up a host</span></span>

<span data-ttu-id="0d725-382">Ana bilgisayar genellikle sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="0d725-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0d725-383">`Main`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0d725-383">The `Main` method:</span></span>

* <span data-ttu-id="0d725-384">`CreateHostBuilder`Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0d725-385">`Build` `Run` Oluşturucu nesnesindeki çağrılar ve Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="0d725-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0d725-386">ASP.NET Core Web şablonları, genel bir konak oluşturmak için aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0d725-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="0d725-387">Aşağıdaki kod, HTTP olmayan iş yükünü kullanan genel bir konak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0d725-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="0d725-388">`IHostedService`Uygulama, dı kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="0d725-388">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="0d725-389">Bir HTTP iş yükü için `Main` yöntem aynıdır ancak çağrılır `CreateHostBuilder` `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="0d725-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0d725-390">Yukarıdaki kod ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0d725-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="0d725-391">Uygulama Entity Framework Core kullanıyorsa, yöntemin adını veya imzasını değiştirmeyin `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0d725-392">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , `CreateHostBuilder` uygulamayı çalıştırmadan Konağı yapılandıran bir yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0d725-393">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0d725-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0d725-394">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="0d725-394">Default builder settings</span></span>

<span data-ttu-id="0d725-395"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0d725-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="0d725-396">[İçerik kökünü](xref:fundamentals/index#content-root) tarafından döndürülen yola ayarlar <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="0d725-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="0d725-397">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0d725-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="0d725-398">Ön eki olan ortam değişkenleri `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0d725-399">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-399">Command-line arguments.</span></span>
* <span data-ttu-id="0d725-400">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="0d725-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="0d725-401">\* Üzerindeappsettings.js\*.</span><span class="sxs-lookup"><span data-stu-id="0d725-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="0d725-402">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0d725-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0d725-403">Uygulama ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) `Development` .</span><span class="sxs-lookup"><span data-stu-id="0d725-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0d725-404">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-404">Environment variables.</span></span>
  * <span data-ttu-id="0d725-405">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-405">Command-line arguments.</span></span>
* <span data-ttu-id="0d725-406">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="0d725-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0d725-407">Konsol</span><span class="sxs-lookup"><span data-stu-id="0d725-407">Console</span></span>
  * <span data-ttu-id="0d725-408">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="0d725-408">Debug</span></span>
  * <span data-ttu-id="0d725-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d725-409">EventSource</span></span>
  * <span data-ttu-id="0d725-410">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="0d725-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0d725-411">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0d725-412">`ConfigureWebHostDefaults`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0d725-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0d725-413">Ön ekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0d725-414">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0d725-415">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="0d725-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0d725-416">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0d725-417">Eşitse, [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="0d725-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0d725-418">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-418">Enables IIS integration.</span></span> <span data-ttu-id="0d725-419">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="0d725-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0d725-420">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0d725-421">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0d725-421">Framework-provided services</span></span>

<span data-ttu-id="0d725-422">Aşağıdaki hizmetler otomatik olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0d725-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0d725-423">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0d725-424">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0d725-425">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="0d725-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0d725-426">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection#framework-provided-services> ..</span><span class="sxs-lookup"><span data-stu-id="0d725-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0d725-427">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="0d725-428"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> `IApplicationLifetime` Başlatma sonrası ve düzgün kapanma görevlerini işlemek için herhangi bir sınıfa (eski adıyla) hizmetini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d725-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0d725-429">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="0d725-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0d725-430">Arabirim Ayrıca bir yöntemi içerir `StopApplication` .</span><span class="sxs-lookup"><span data-stu-id="0d725-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0d725-431">Aşağıdaki örnek, `IHostedService` olayları kaydeden bir uygulamasıdır `IHostApplicationLifetime` :</span><span class="sxs-lookup"><span data-stu-id="0d725-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0d725-432">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-432">IHostLifetime</span></span>

<span data-ttu-id="0d725-433"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler.</span><span class="sxs-lookup"><span data-stu-id="0d725-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0d725-434">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-434">The last implementation registered is used.</span></span>

<span data-ttu-id="0d725-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan `IHostLifetime` uygulama.</span><span class="sxs-lookup"><span data-stu-id="0d725-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0d725-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0d725-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0d725-437"><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="0d725-438">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0d725-439">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="0d725-439">IHostEnvironment</span></span>

<span data-ttu-id="0d725-440"><xref:Microsoft.Extensions.Hosting.IHostEnvironment>Aşağıdaki ayarlarla ilgili bilgi almak için hizmeti bir sınıfa ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0d725-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0d725-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0d725-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0d725-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0d725-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0d725-443">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="0d725-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0d725-444">Web Apps, `IWebHostEnvironment` WebRootPath öğesini devralan `IHostEnvironment` ve ekleyen arabirimini uygular. [WebRootPath](#webroot)</span><span class="sxs-lookup"><span data-stu-id="0d725-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0d725-445">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0d725-445">Host configuration</span></span>

<span data-ttu-id="0d725-446">Ana bilgisayar yapılandırması, uygulamanın özellikleri için kullanılır <xref:Microsoft.Extensions.Hosting.IHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="0d725-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0d725-447">Ana Bilgisayar Yapılandırması içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) tarafından kullanılabilir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="0d725-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="0d725-448">Sonra `ConfigureAppConfiguration` , `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0d725-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0d725-449">Konak yapılandırması eklemek için üzerinde öğesini <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> çağırın `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="0d725-450">`ConfigureHostConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0d725-451">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0d725-452">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı tarafından dahildir `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0d725-453">Web Apps için, ön ekine sahip ortam değişkeni sağlayıcısı `ASPNETCORE_` eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0d725-454">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0d725-455">Örneğin, için ortam değişkeni değeri, `ASPNETCORE_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="0d725-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0d725-456">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0d725-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0d725-457">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0d725-457">App configuration</span></span>

<span data-ttu-id="0d725-458">Uygulama yapılandırması, üzerinde çağırarak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0d725-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0d725-459">`ConfigureAppConfiguration` , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0d725-460">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0d725-461">Tarafından oluşturulan yapılandırma, `ConfigureAppConfiguration` sonraki işlemler ve dı hizmeti olarak [HostBuilderContext.Config](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bir hizmet olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0d725-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0d725-462">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0d725-463">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="0d725-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0d725-464">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="0d725-464">Settings for all app types</span></span>

<span data-ttu-id="0d725-465">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="0d725-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0d725-466">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0d725-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0d725-467">ApplicationName</span></span>

<span data-ttu-id="0d725-468">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0d725-469">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="0d725-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0d725-470">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-470">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-471">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="0d725-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0d725-472">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0d725-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0d725-473">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d725-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0d725-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-474">ContentRoot</span></span>

<span data-ttu-id="0d725-475">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="0d725-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0d725-476">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="0d725-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0d725-477">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0d725-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0d725-478">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-478">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-479">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="0d725-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0d725-480">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0d725-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0d725-481">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseContentRoot` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0d725-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0d725-482">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0d725-482">For more information, see:</span></span>

* [<span data-ttu-id="0d725-483">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="0d725-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0d725-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0d725-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0d725-485">EnvironmentName</span></span>

<span data-ttu-id="0d725-486">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0d725-487">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="0d725-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0d725-488">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0d725-489">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="0d725-489">**Key**: `environment`</span></span>  
<span data-ttu-id="0d725-490">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-490">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-491">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="0d725-491">**Default**: `Production`</span></span>  
<span data-ttu-id="0d725-492">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0d725-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0d725-493">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseEnvironment` yapın `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0d725-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0d725-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0d725-494">ShutdownTimeout</span></span>

<span data-ttu-id="0d725-495">[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0d725-496">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-496">The default value is five seconds.</span></span>  <span data-ttu-id="0d725-497">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="0d725-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="0d725-498">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="0d725-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0d725-499">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0d725-500">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="0d725-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0d725-501">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="0d725-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0d725-502">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="0d725-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0d725-503">**Anahtar**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0d725-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0d725-504">**Şunu yazın**: `int`</span><span class="sxs-lookup"><span data-stu-id="0d725-504">**Type**: `int`</span></span>  
<span data-ttu-id="0d725-505">**Varsayılan**: 5 saniye</span><span class="sxs-lookup"><span data-stu-id="0d725-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0d725-506">**Ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0d725-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0d725-507">Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="0d725-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0d725-508">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0d725-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="0d725-509">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="0d725-509">Settings for web apps</span></span>

<span data-ttu-id="0d725-510">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0d725-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0d725-511">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya öneki olabilir `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0d725-512">Üzerinde uzantı yöntemleri `IWebHostBuilder` Bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0d725-513">`webBuilder` `IWebHostBuilder` Aşağıdaki örnekte olduğu gibi, ' nin bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:</span><span class="sxs-lookup"><span data-stu-id="0d725-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0d725-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0d725-514">CaptureStartupErrors</span></span>

<span data-ttu-id="0d725-515">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="0d725-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0d725-516">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0d725-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0d725-517">**Anahtar**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0d725-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0d725-518">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-519">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="0d725-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0d725-520">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0d725-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0d725-521">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="0d725-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0d725-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0d725-522">DetailedErrors</span></span>

<span data-ttu-id="0d725-523">Etkinleştirildiğinde veya ortam olduğunda, `Development` uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="0d725-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0d725-524">**Anahtar**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0d725-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0d725-525">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-526">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="0d725-526">**Default**: `false`</span></span>  
<span data-ttu-id="0d725-527">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0d725-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0d725-528">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0d725-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0d725-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="0d725-530">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0d725-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0d725-531">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="0d725-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0d725-532">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0d725-533">**Anahtar**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0d725-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0d725-534">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-534">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-535">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0d725-535">**Default**: Empty string</span></span>  
<span data-ttu-id="0d725-536">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0d725-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0d725-537">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0d725-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0d725-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0d725-539">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="0d725-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0d725-540">**Anahtar**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0d725-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0d725-541">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-541">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-542">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="0d725-542">**Default**: Empty string</span></span>  
<span data-ttu-id="0d725-543">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0d725-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0d725-544">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0d725-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0d725-545">HTTPS_Port</span></span>

<span data-ttu-id="0d725-546">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="0d725-546">The HTTPS redirect port.</span></span> <span data-ttu-id="0d725-547">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0d725-548">**Anahtar**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="0d725-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="0d725-549">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-549">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-550">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0d725-551">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0d725-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0d725-552">Bu değeri ayarlamak için yapılandırma veya çağırma kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0d725-553">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="0d725-553">PreferHostingUrls</span></span>

<span data-ttu-id="0d725-554">Konağın uygulamayla yapılandırılmış URL 'Ler yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IWebHostBuilder` `IServer` .</span><span class="sxs-lookup"><span data-stu-id="0d725-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0d725-555">**Anahtar**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0d725-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0d725-556">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-557">**Varsayılan**: `true`</span><span class="sxs-lookup"><span data-stu-id="0d725-557">**Default**: `true`</span></span>  
<span data-ttu-id="0d725-558">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0d725-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0d725-559">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="0d725-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0d725-560">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="0d725-560">PreventHostingStartup</span></span>

<span data-ttu-id="0d725-561">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="0d725-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0d725-562">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0d725-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0d725-563">**Anahtar**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0d725-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0d725-564">**Tür**: `bool` ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="0d725-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0d725-565">**Varsayılan**: `false`</span><span class="sxs-lookup"><span data-stu-id="0d725-565">**Default**: `false`</span></span>  
<span data-ttu-id="0d725-566">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0d725-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0d725-567">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0d725-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0d725-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0d725-568">StartupAssembly</span></span>

<span data-ttu-id="0d725-569">Sınıfı aramak için bütünleştirilmiş kod `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0d725-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0d725-570">**Anahtar**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0d725-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0d725-571">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-571">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-572">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="0d725-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0d725-573">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0d725-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0d725-574">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="0d725-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0d725-575">`UseStartup` bir derleme adı ( `string` ) veya bir tür () alabilir `TStartup` .</span><span class="sxs-lookup"><span data-stu-id="0d725-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0d725-576">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="0d725-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0d725-577">URL’ler</span><span class="sxs-lookup"><span data-stu-id="0d725-577">URLs</span></span>

<span data-ttu-id="0d725-578">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0d725-579">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0d725-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0d725-580">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="0d725-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0d725-581">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d725-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0d725-582">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="0d725-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="0d725-583">**Anahtar**: `urls`</span><span class="sxs-lookup"><span data-stu-id="0d725-583">**Key**: `urls`</span></span>  
<span data-ttu-id="0d725-584">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-584">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-585">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0d725-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0d725-586">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0d725-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0d725-587">Bu değeri ayarlamak için, ortam değişkenini veya çağrısını kullanın `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="0d725-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0d725-588">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0d725-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0d725-589">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0d725-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0d725-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-590">WebRoot</span></span>

<span data-ttu-id="0d725-591">[Iwebhostenvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği, uygulamanın statik varlıklarının göreli yolunu belirler.</span><span class="sxs-lookup"><span data-stu-id="0d725-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0d725-592">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0d725-593">**Anahtar**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="0d725-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="0d725-594">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-594">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-595">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0d725-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0d725-596">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d725-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0d725-597">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0d725-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0d725-598">Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde arama `UseWebRoot` yapın `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0d725-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0d725-599">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0d725-599">For more information, see:</span></span>

* [<span data-ttu-id="0d725-600">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="0d725-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0d725-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0d725-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0d725-602">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="0d725-602">Manage the host lifetime</span></span>

<span data-ttu-id="0d725-603"><xref:Microsoft.Extensions.Hosting.IHost>Uygulamayı başlatmak ve durdurmak için oluşturulan uygulamadaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="0d725-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0d725-604">Bu yöntemler  <xref:Microsoft.Extensions.Hosting.IHostedService> , hizmet kapsayıcısına kayıtlı tüm uygulamaları etkiler.</span><span class="sxs-lookup"><span data-stu-id="0d725-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0d725-605">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="0d725-605">Run</span></span>

<span data-ttu-id="0d725-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0d725-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0d725-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-607">RunAsync</span></span>

<span data-ttu-id="0d725-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0d725-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0d725-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-609">RunConsoleAsync</span></span>

<span data-ttu-id="0d725-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0d725-611">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="0d725-611">Start</span></span>

<span data-ttu-id="0d725-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="0d725-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0d725-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-613">StartAsync</span></span>

<span data-ttu-id="0d725-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Konağı başlatır ve <xref:System.Threading.Tasks.Task> iptal belirteci ya da kapatması tetiklendiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="0d725-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0d725-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır `StartAsync` ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0d725-616">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0d725-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-617">StopAsync</span></span>

<span data-ttu-id="0d725-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0d725-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0d725-619">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="0d725-619">WaitForShutdown</span></span>

<span data-ttu-id="0d725-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="0d725-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0d725-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="0d725-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0d725-623">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="0d725-623">External control</span></span>

<span data-ttu-id="0d725-624">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="0d725-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0d725-625">ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="0d725-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="0d725-626">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="0d725-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="0d725-627">Bu makalede <xref:Microsoft.Extensions.Hosting.HostBuilder> , http isteklerini işlemeyecek uygulamalar için kullanılan genel ana bilgisayar () ASP.NET Core ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0d725-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="0d725-628">Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="0d725-629">Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.</span><span class="sxs-lookup"><span data-stu-id="0d725-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="0d725-630">Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="0d725-631">Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d725-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="0d725-632">Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.</span><span class="sxs-lookup"><span data-stu-id="0d725-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="0d725-633">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d725-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0d725-634">Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal*kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d725-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="0d725-635">Örneği bir içinde çalıştırmayın `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="0d725-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="0d725-636">Konsolu Visual Studio Code ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="0d725-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="0d725-637">Dosyasında *. vscode/launch.js* açın.</span><span class="sxs-lookup"><span data-stu-id="0d725-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="0d725-638">**.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="0d725-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="0d725-639">Değeri ya da olarak ayarlayın `externalTerminal` `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="0d725-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="0d725-640">Giriş</span><span class="sxs-lookup"><span data-stu-id="0d725-640">Introduction</span></span>

<span data-ttu-id="0d725-641">Genel konak kitaplığı ad alanında kullanılabilir <xref:Microsoft.Extensions.Hosting> ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="0d725-642">`Microsoft.Extensions.Hosting`Paket, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="0d725-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="0d725-643"><xref:Microsoft.Extensions.Hosting.IHostedService> , kod yürütmeye yönelik giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="0d725-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="0d725-644">Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices içinde hizmet kaydı](#configureservices)sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="0d725-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="0d725-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> , <xref:Microsoft.Extensions.Hosting.IHostedService> konak başlatıldığında çağrılır ve <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ana bilgisayar düzgün bir şekilde kapandığında ters kayıt sırasında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0d725-646">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="0d725-646">Set up a host</span></span>

<span data-ttu-id="0d725-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> , kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="0d725-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="0d725-648">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="0d725-648">Options</span></span>

<span data-ttu-id="0d725-649"><xref:Microsoft.Extensions.Hosting.HostOptions> için seçenekleri yapılandırın <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="0d725-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="0d725-650">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="0d725-650">Shutdown timeout</span></span>

<span data-ttu-id="0d725-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> zaman aşımını ayarlar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0d725-652">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="0d725-652">The default value is five seconds.</span></span>

<span data-ttu-id="0d725-653">İçindeki aşağıdaki seçenek yapılandırması, `Program.Main` varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:</span><span class="sxs-lookup"><span data-stu-id="0d725-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="0d725-654">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0d725-654">Default services</span></span>

<span data-ttu-id="0d725-655">Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0d725-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="0d725-656">[Ortam](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="0d725-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="0d725-657">[Yapılandırma](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="0d725-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="0d725-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0d725-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="0d725-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0d725-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="0d725-660">[Seçenekler](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="0d725-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="0d725-661">[Günlüğe kaydetme](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="0d725-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0d725-662">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0d725-662">Host configuration</span></span>

<span data-ttu-id="0d725-663">Ana bilgisayar yapılandırması şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0d725-663">Host configuration is created by:</span></span>

* <span data-ttu-id="0d725-664"><xref:Microsoft.Extensions.Hosting.IHostBuilder> [İçerik kökünü](#content-root) ve [ortamı](#environment)ayarlamak için uzantı yöntemleri çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="0d725-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="0d725-665">İçindeki yapılandırma sağlayıcılarından yapılandırma okunuyor <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="0d725-666">Genişletme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0d725-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="0d725-667">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="0d725-667">Application key (name)</span></span>

<span data-ttu-id="0d725-668">[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="0d725-669">Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="0d725-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="0d725-670">**Anahtar**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="0d725-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0d725-671">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-671">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-672">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="0d725-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="0d725-673">Şunu **kullanarak ayarla**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="0d725-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="0d725-674">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0d725-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="0d725-675">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="0d725-675">Content root</span></span>

<span data-ttu-id="0d725-676">Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="0d725-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="0d725-677">**Anahtar**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0d725-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0d725-678">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-678">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-679">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="0d725-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="0d725-680">Şunu **kullanarak ayarla**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="0d725-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="0d725-681">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0d725-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0d725-682">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="0d725-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="0d725-683">Daha fazla bilgi için bkz. [temel bilgiler: içerik kökü](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="0d725-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="0d725-684">Ortam</span><span class="sxs-lookup"><span data-stu-id="0d725-684">Environment</span></span>

<span data-ttu-id="0d725-685">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0d725-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0d725-686">**Anahtar**: `environment`</span><span class="sxs-lookup"><span data-stu-id="0d725-686">**Key**: `environment`</span></span>  
<span data-ttu-id="0d725-687">**Şunu yazın**: `string`</span><span class="sxs-lookup"><span data-stu-id="0d725-687">**Type**: `string`</span></span>  
<span data-ttu-id="0d725-688">**Varsayılan**: `Production`</span><span class="sxs-lookup"><span data-stu-id="0d725-688">**Default**: `Production`</span></span>  
<span data-ttu-id="0d725-689">Şunu **kullanarak ayarla**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="0d725-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="0d725-690">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0d725-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0d725-691">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-691">The environment can be set to any value.</span></span> <span data-ttu-id="0d725-692">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="0d725-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0d725-693">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="0d725-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="0d725-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="0d725-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>konak için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="0d725-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="0d725-696">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın derleme sürecinde kullanılmak üzere başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="0d725-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0d725-698">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0d725-699">Varsayılan olarak hiçbir sağlayıcı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="0d725-699">No providers are included by default.</span></span> <span data-ttu-id="0d725-700">Uygulamanın gerektirdiği her türlü yapılandırma sağlayıcısını açık bir şekilde belirtmeniz gerekir <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , örneğin:</span><span class="sxs-lookup"><span data-stu-id="0d725-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="0d725-701">Dosya yapılandırması (örneğin, dosyadaki bir *hostsettings.js* ).</span><span class="sxs-lookup"><span data-stu-id="0d725-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="0d725-702">Ortam değişkeni yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="0d725-702">Environment variable configuration.</span></span>
* <span data-ttu-id="0d725-703">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="0d725-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="0d725-704">Diğer tüm gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="0d725-704">Any other required configuration providers.</span></span>

<span data-ttu-id="0d725-705">Konağın dosya yapılandırması, uygulamanın temel yolu ve `SetBasePath` ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="0d725-706">Örnek uygulama, \* üzerindehostsettings.js\*bir JSON dosyası ve <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> dosyanın ana bilgisayar yapılandırma ayarlarını kullanmak için çağrılar kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="0d725-707">Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak Oluşturucu ' ya çağrı yapın <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="0d725-708">`AddEnvironmentVariables` Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="0d725-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="0d725-709">Örnek uygulama, öneki kullanır `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="0d725-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="0d725-710">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0d725-711">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, için ortam değişkeni değeri `PREFIX_ENVIRONMENT` anahtar için ana bilgisayar yapılandırma değeri olur `environment` .</span><span class="sxs-lookup"><span data-stu-id="0d725-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0d725-712">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya ile bir uygulama çalıştırırken geliştirme sırasında `dotnet run` , ortam değişkenleri dosyadaki *Özellikler/launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="0d725-713">[Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında dosya *üzerinde. vscode/launch.js* içinde ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="0d725-714">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0d725-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0d725-715">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağırarak eklenir <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="0d725-716">Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="0d725-717">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="0d725-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="0d725-718">Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="0d725-719">`HostBuilder`Kullanarak örnek yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="0d725-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="0d725-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="0d725-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="0d725-721">Uygulama yapılandırması, uygulama çağrısı yaparak oluşturulur <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0d725-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="0d725-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>uygulama için oluşturmak üzere bir kullanır <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="0d725-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="0d725-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0d725-724">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d725-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="0d725-725">Tarafından oluşturulan yapılandırma, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sonraki işlemler ve içinde [HostBuilderContext.Configurlama](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) bölümünde mevcuttur <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="0d725-726">Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="0d725-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="0d725-727">Kullanarak örnek uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="0d725-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="0d725-728">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="0d725-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="0d725-729">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="0d725-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="0d725-730">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="0d725-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="0d725-731">Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="0d725-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="0d725-732">Örnek uygulama, JSON uygulama ayarları dosyalarını ve *hostsettings.js* aşağıdaki öğeyle taşıdıkça `<Content>` :</span><span class="sxs-lookup"><span data-stu-id="0d725-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="0d725-733">Ve gibi yapılandırma uzantısı yöntemleri, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.Js](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) veMicrosoft.Extensions.Configuration gibi ek NuGet paketleri gerektirir [ . EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="0d725-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="0d725-734">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadıkça, bu paketlerin çekirdek [Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d725-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="0d725-735">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0d725-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="0d725-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="0d725-736">ConfigureServices</span></span>

<span data-ttu-id="0d725-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0d725-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0d725-739">Barındırılan hizmet, arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="0d725-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0d725-740">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0d725-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="0d725-741">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `AddHostedService` yaşam süresi olayları, `LifetimeEventsHostedService` ve zamanlanan bir arka plan görevi için uygulamaya bir hizmet eklemek için genişletme yöntemini kullanır `TimedHostedService` :</span><span class="sxs-lookup"><span data-stu-id="0d725-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="0d725-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="0d725-742">ConfigureLogging</span></span>

<span data-ttu-id="0d725-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> belirtilen yapılandırmayı yapılandırmak için bir temsilci ekler <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0d725-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="0d725-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="0d725-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="0d725-745">UseConsoleLifetime</span></span>

<span data-ttu-id="0d725-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'i dinler ve <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> kapalı işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="0d725-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="0d725-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` Varsayılan yaşam süresi uygulamasına önceden kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="0d725-749">Kaydedilen son yaşam süresi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d725-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="0d725-750">Kapsayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="0d725-750">Container configuration</span></span>

<span data-ttu-id="0d725-751">Diğer kapsayıcıları takmayı desteklemek için ana bilgisayar bir kabul edebilir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="0d725-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="0d725-752">Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır.</span><span class="sxs-lookup"><span data-stu-id="0d725-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="0d725-753">[Useserviceproviderfactory (ıvıceproviderfactory &lt; tcontainerbuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0d725-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="0d725-754">Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemiyle yönetilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="0d725-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d725-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="0d725-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> , eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0d725-757">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0d725-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="0d725-758">Hizmet kapsayıcısı fabrikası sağlama:</span><span class="sxs-lookup"><span data-stu-id="0d725-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="0d725-759">Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="0d725-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="0d725-760">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="0d725-760">Extensibility</span></span>

<span data-ttu-id="0d725-761">Konak genişletilebilirliği, üzerindeki genişletme yöntemleriyle gerçekleştirilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0d725-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="0d725-762">Aşağıdaki örnek, bir genişletme yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> ' de gösterilen [Timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir uygulamayı nasıl genişlettiğini gösterir <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="0d725-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="0d725-763">Uygulama, `UseHostedService` geçirilen barındırılan hizmeti kaydetmek için uzantı yöntemi oluşturur `T` :</span><span class="sxs-lookup"><span data-stu-id="0d725-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="0d725-764">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="0d725-764">Manage the host</span></span>

<span data-ttu-id="0d725-765"><xref:Microsoft.Extensions.Hosting.IHost>Uygulama, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı olan uygulamaları başlatıp durdurmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="0d725-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0d725-766">Çalıştır</span><span class="sxs-lookup"><span data-stu-id="0d725-766">Run</span></span>

<span data-ttu-id="0d725-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="0d725-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="0d725-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-768">RunAsync</span></span>

<span data-ttu-id="0d725-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve <xref:System.Threading.Tasks.Task> iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="0d725-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="0d725-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-770">RunConsoleAsync</span></span>

<span data-ttu-id="0d725-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteğini sağlar, Konağı oluşturur ve başlatır ve <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterm 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="0d725-772">Başlangıç ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-772">Start and StopAsync</span></span>

<span data-ttu-id="0d725-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="0d725-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="0d725-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0d725-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="0d725-775">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="0d725-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="0d725-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="0d725-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> uygulamayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="0d725-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="0d725-778">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="0d725-778">WaitForShutdown</span></span>

<span data-ttu-id="0d725-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, (örneğin, <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` <kbd>CTRL</kbd> + <kbd>C</kbd>/sigint veya sigterim dinler) ile tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="0d725-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="0d725-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> çağırır <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="0d725-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0d725-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="0d725-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task>verilen belirteç ve çağrılar aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0d725-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="0d725-783">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="0d725-783">External control</span></span>

<span data-ttu-id="0d725-784">Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:</span><span class="sxs-lookup"><span data-stu-id="0d725-784">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0d725-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> , ' nin başlangıcında çağrılır <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ve devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="0d725-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0d725-786">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="0d725-787">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="0d725-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="0d725-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d725-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="0d725-789">Özelliklerini ve uzantı yöntemlerini kullanmak üzere sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> :</span><span class="sxs-lookup"><span data-stu-id="0d725-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="0d725-790">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0d725-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="0d725-791">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="0d725-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="0d725-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> düzgün kapanma istekleri de dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d725-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="0d725-793">Arabirimdeki üç özellik, <xref:System.Action> Başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="0d725-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="0d725-794">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="0d725-794">Cancellation Token</span></span> | <span data-ttu-id="0d725-795">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="0d725-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="0d725-796">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="0d725-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="0d725-797">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="0d725-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="0d725-798">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d725-798">All requests should be processed.</span></span> <span data-ttu-id="0d725-799">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="0d725-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="0d725-800">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="0d725-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="0d725-801">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="0d725-801">Requests may still be processing.</span></span> <span data-ttu-id="0d725-802">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="0d725-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="0d725-803">Constructor- <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmeti herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d725-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="0d725-804">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , `LifetimeEventsHostedService` olayları kaydetmek için bir sınıfa (bir uygulama) ekleme oluşturucusunu kullanır <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="0d725-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="0d725-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="0d725-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="0d725-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="0d725-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="0d725-807">Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır `Shutdown` :</span><span class="sxs-lookup"><span data-stu-id="0d725-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0d725-808">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0d725-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
