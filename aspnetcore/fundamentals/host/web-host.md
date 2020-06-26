---
title: ASP.NET Core Web ana bilgisayarı
author: rick-anderson
description: Uygulama başlatma ve ömür yönetiminden sorumlu olan ASP.NET Core Web ana bilgisayarı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/web-host
ms.openlocfilehash: 630191948a9013e88853ee1a31d15f2964b4a7f4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399419"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="f0519-103">ASP.NET Core Web ana bilgisayarı</span><span class="sxs-lookup"><span data-stu-id="f0519-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="f0519-104">ASP.NET Core uygulamalar bir *Konağı*yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="f0519-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="f0519-105">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="f0519-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="f0519-106">Ana bilgisayar, en azından bir sunucu ve bir istek işleme işlem hattı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f0519-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="f0519-107">Konak, günlüğe kaydetme, bağımlılık ekleme ve yapılandırma de ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0519-108">Bu makalede, yalnızca geriye dönük uyumluluk için kullanılabilen Web ana bilgisayarı ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f0519-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="f0519-109">[Genel ana bilgisayar](xref:fundamentals/host/generic-host) tüm uygulama türleri için önerilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0519-110">Bu makale, Web uygulamalarını barındırmak için olan Web konağını ele alır.</span><span class="sxs-lookup"><span data-stu-id="f0519-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="f0519-111">Diğer uygulama türleri için [genel Konağı](xref:fundamentals/host/generic-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f0519-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="f0519-112">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="f0519-112">Set up a host</span></span>

<span data-ttu-id="f0519-113">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)'ın bir örneğini kullanarak bir konak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f0519-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="f0519-114">Bu, genellikle uygulamanın giriş noktasında ve yöntemi olarak gerçekleştirilir `Main` .</span><span class="sxs-lookup"><span data-stu-id="f0519-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="f0519-115">Proje şablonlarında, `Main` *program.cs*konumunda bulunur.</span><span class="sxs-lookup"><span data-stu-id="f0519-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="f0519-116">Tipik bir uygulama, bir konak ayarlamaya başlamak için [Createdefaultbuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) çağırır:</span><span class="sxs-lookup"><span data-stu-id="f0519-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f0519-117">Çağıran kod, `CreateDefaultBuilder` `CreateWebHostBuilder` `Main` Oluşturucu nesnesindeki çağrılarındaki koddan ayıran adlı bir yöntemde bulunur `Run` .</span><span class="sxs-lookup"><span data-stu-id="f0519-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="f0519-118">[Entity Framework Core araçlarını](/ef/core/miscellaneous/cli/)kullanıyorsanız bu ayrım gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f0519-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="f0519-119">Araçlar, `CreateWebHostBuilder` uygulamayı çalıştırmadan Konağı yapılandırmak için tasarım zamanında çağırabilecekleri bir yöntemi bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="f0519-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="f0519-120">Diğer bir seçenek de uygulanır `IDesignTimeDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="f0519-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="f0519-121">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="f0519-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="f0519-122">`CreateDefaultBuilder` aşağıdaki görevleri gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="f0519-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="f0519-123">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu, uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak Web sunucusu olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f0519-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="f0519-124">Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options> ..</span><span class="sxs-lookup"><span data-stu-id="f0519-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="f0519-125">[İçerik kökünü](xref:fundamentals/index#content-root) [Directory. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)tarafından döndürülen yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f0519-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="f0519-126">[Ana bilgisayar yapılandırmasını](#host-configuration-values) şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="f0519-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="f0519-127">Ön eki olan ortam değişkenleri `ASPNETCORE_` (örneğin, `ASPNETCORE_ENVIRONMENT` ).</span><span class="sxs-lookup"><span data-stu-id="f0519-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="f0519-128">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f0519-128">Command-line arguments.</span></span>
* <span data-ttu-id="f0519-129">Aşağıdaki sırayla uygulama yapılandırmasını yükler:</span><span class="sxs-lookup"><span data-stu-id="f0519-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="f0519-130">*Üzerindeappsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="f0519-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="f0519-131">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f0519-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="f0519-132">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="f0519-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="f0519-133">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f0519-133">Environment variables.</span></span>
  * <span data-ttu-id="f0519-134">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f0519-134">Command-line arguments.</span></span>
* <span data-ttu-id="f0519-135">Konsol ve hata ayıklama çıkışı için [günlüğe kaydetmeyi](xref:fundamentals/logging/index) yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f0519-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="f0519-136">Günlüğe kaydetme, bir *appsettings.json* veya appSettings 'in günlük yapılandırma bölümünde belirtilen [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) kurallarını içerir *. { Environment}. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="f0519-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="f0519-137">[ASP.NET Core modülle](xref:host-and-deploy/aspnet-core-module)IIS 'nin arkasında çalışırken, `CreateDefaultBuilder` uygulamanın temel adresini ve bağlantı noktasını yapılandıran [IIS tümleştirmesini](xref:host-and-deploy/iis/index)da sunar.</span><span class="sxs-lookup"><span data-stu-id="f0519-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="f0519-138">IIS tümleştirmesi, uygulamayı [başlatma hatalarını yakalamaya](#capture-startup-errors)de yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f0519-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="f0519-139">IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options> ..</span><span class="sxs-lookup"><span data-stu-id="f0519-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="f0519-140">Uygulamanın ortamı geliştirme ortamındaysanız, [Serviceprovideroptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) öğesini olarak ayarlar `true` .</span><span class="sxs-lookup"><span data-stu-id="f0519-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="f0519-141">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="f0519-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="f0519-142">Tarafından tanımlanan yapılandırma `CreateDefaultBuilder` , [Configureappconfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [configurelogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)ve [ıwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)'ın diğer yöntemleri ve genişletme yöntemleri tarafından geçersiz kılınabilir ve genişletilebilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="f0519-143">Birkaç örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f0519-143">A few examples follow:</span></span>

* <span data-ttu-id="f0519-144">[Configureappconfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) , uygulama için ek belirtmek için kullanılır `IConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="f0519-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="f0519-145">Aşağıdaki `ConfigureAppConfiguration` çağrı *appsettings.xml* dosyasına uygulama yapılandırmasını dahil etmek için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="f0519-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="f0519-146">`ConfigureAppConfiguration`birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="f0519-147">Bu yapılandırmanın ana bilgisayar için (örneğin, sunucu URL 'Leri veya ortam) uygulanmadığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="f0519-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="f0519-148">[Konak yapılandırma değerleri](#host-configuration-values) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f0519-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="f0519-149">Aşağıdaki `ConfigureLogging` çağrı, en düşük günlük düzeyi ([Setminimumlevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) değerini [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel)olarak yapılandırmak için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="f0519-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="f0519-150">Bu ayar, () *üzerindeappsettings.Development.js* `LogLevel.Debug` ve tarafından yapılandırılan () *appsettings.Production.jsüzerindeki* ayarları geçersiz kılar `LogLevel.Error` `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f0519-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f0519-151">`ConfigureLogging`birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f0519-152">Aşağıdaki çağrı `ConfigureKestrel` varsayılan limitleri geçersiz kılar [. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) , Kestrel yapılandırıldığında oluşturulan 30.000.000 bayt `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="f0519-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f0519-153">Aşağıdaki [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) çağrısı, varsayılan limitleri geçersiz kılar [. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) , Kestrel tarafından yapılandırıldığında oluşturulan 30.000.000 bayttan oluşur `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="f0519-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="f0519-154">[İçerik kökü](xref:fundamentals/index#content-root) , konağın MVC görünüm dosyaları gibi içerik dosyalarını arayacağı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="f0519-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="f0519-155">Uygulama, projenin kök klasöründen başlatıldığında, projenin kök klasörü içerik kökü olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0519-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="f0519-156">Bu, [Visual Studio](https://visualstudio.microsoft.com) 'da ve [DotNet yeni şablonlarda](/dotnet/core/tools/dotnet-new)kullanılan varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="f0519-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="f0519-157">Uygulama yapılandırması hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="f0519-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="f0519-158">Statik yöntemin kullanılmasına alternatif olarak `CreateDefaultBuilder` , [Webhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 'dan bir konak oluşturmak, ASP.NET Core 2. x ile desteklenen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="f0519-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="f0519-159">Bir ana bilgisayar ayarlanırken, [yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) ve [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) yöntemleri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="f0519-160">Bir `Startup` sınıf belirtilmişse, bir `Configure` yöntemi tanımlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f0519-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="f0519-161">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f0519-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="f0519-162">Birbirine eklenecek birden çok çağrı `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f0519-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="f0519-163">`Configure` `UseStartup` Önceki ayarları değiştir üzerinde veya üzerine birden çok çağrı `WebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f0519-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="f0519-164">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="f0519-164">Host configuration values</span></span>

<span data-ttu-id="f0519-165">[Webhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) , ana bilgisayar yapılandırma değerlerini ayarlamak için aşağıdaki yaklaşımları kullanır:</span><span class="sxs-lookup"><span data-stu-id="f0519-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="f0519-166">Bu biçimdeki ortam değişkenlerini içeren konak Oluşturucu yapılandırması `ASPNETCORE_{configurationKey}` .</span><span class="sxs-lookup"><span data-stu-id="f0519-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="f0519-167">Örneğin, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="f0519-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="f0519-168">[Usecontentroot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) ve [useconfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) gibi uzantılar ( [geçersiz kılma yapılandırması](#override-configuration) bölümüne bakın).</span><span class="sxs-lookup"><span data-stu-id="f0519-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="f0519-169">[Usesetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) ve ilişkili anahtar.</span><span class="sxs-lookup"><span data-stu-id="f0519-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="f0519-170">İle bir değer ayarlarken `UseSetting` , değer türünden bağımsız olarak bir dize olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f0519-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="f0519-171">Konak, bir değeri en son ayarlayan seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="f0519-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="f0519-172">Daha fazla bilgi için, sonraki bölümde [yapılandırmayı geçersiz kılma](#override-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f0519-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="f0519-173">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="f0519-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0519-174">`IWebHostEnvironment.ApplicationName`Özelliği, konak oluşturma sırasında [usestartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) veya [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) çağrıldığında otomatik olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f0519-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="f0519-175">Değer, uygulamanın giriş noktasını içeren derlemenin adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f0519-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="f0519-176">Değeri açıkça ayarlamak için [Webhostdefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="f0519-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0519-177">Konak oluşturma sırasında [Usestartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) veya [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) çağrıldığında, [ıhostingenvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) özelliği otomatik olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f0519-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="f0519-178">Değer, uygulamanın giriş noktasını içeren derlemenin adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f0519-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="f0519-179">Değeri açıkça ayarlamak için [Webhostdefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="f0519-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="f0519-180">**Anahtar**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="f0519-180">**Key**: applicationName</span></span>  
<span data-ttu-id="f0519-181">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-181">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-182">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="f0519-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="f0519-183">Şunu **kullanarak ayarla**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="f0519-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="f0519-184">**Ortam değişkeni**:`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="f0519-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="f0519-185">Yakalama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f0519-185">Capture Startup Errors</span></span>

<span data-ttu-id="f0519-186">Bu ayar, başlatma hatalarının yakalanmasını denetler.</span><span class="sxs-lookup"><span data-stu-id="f0519-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="f0519-187">**Anahtar**: capturestartuperrors</span><span class="sxs-lookup"><span data-stu-id="f0519-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="f0519-188">**Tür**: *bool* ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="f0519-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f0519-189">**Varsayılan**: `false` uygulamanın IIS arkasındaki Kestrel, varsayılan olarak olduğu durumlar dışında çalışır `true` .</span><span class="sxs-lookup"><span data-stu-id="f0519-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="f0519-190">Şunu **kullanarak ayarla**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="f0519-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="f0519-191">**Ortam değişkeni**:`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="f0519-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="f0519-192">Ne zaman `false` , başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor.</span><span class="sxs-lookup"><span data-stu-id="f0519-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="f0519-193">Ne zaman `true` , ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f0519-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="f0519-194">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="f0519-194">Content root</span></span>

<span data-ttu-id="f0519-195">Bu ayar ASP.NET Core içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="f0519-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="f0519-196">**Anahtar**: contentroot</span><span class="sxs-lookup"><span data-stu-id="f0519-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="f0519-197">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-197">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-198">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="f0519-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="f0519-199">Şunu **kullanarak ayarla**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="f0519-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="f0519-200">**Ortam değişkeni**:`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="f0519-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="f0519-201">İçerik kökü, [Web kökünün](xref:fundamentals/index#web-root)temel yolu olarak da kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0519-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="f0519-202">İçerik kök yolu yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="f0519-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="f0519-203">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f0519-203">For more information, see:</span></span>

* [<span data-ttu-id="f0519-204">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="f0519-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="f0519-205">Web kökü</span><span class="sxs-lookup"><span data-stu-id="f0519-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="f0519-206">Ayrıntılı hatalar</span><span class="sxs-lookup"><span data-stu-id="f0519-206">Detailed Errors</span></span>

<span data-ttu-id="f0519-207">Ayrıntılı hataların yakalanıp yakalanmayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="f0519-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="f0519-208">**Anahtar**: detailederrors</span><span class="sxs-lookup"><span data-stu-id="f0519-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="f0519-209">**Tür**: *bool* ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="f0519-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f0519-210">**Varsayılan**: false</span><span class="sxs-lookup"><span data-stu-id="f0519-210">**Default**: false</span></span>  
<span data-ttu-id="f0519-211">Şunu **kullanarak ayarla**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="f0519-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="f0519-212">**Ortam değişkeni**:`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="f0519-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="f0519-213">Etkinleştirildiğinde (veya <a href="#environment">ortam</a> olarak ayarlandığında `Development` ), uygulama ayrıntılı özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="f0519-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="f0519-214">Ortam</span><span class="sxs-lookup"><span data-stu-id="f0519-214">Environment</span></span>

<span data-ttu-id="f0519-215">Uygulamanın ortamını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f0519-215">Sets the app's environment.</span></span>

<span data-ttu-id="f0519-216">**Anahtar**: ortam</span><span class="sxs-lookup"><span data-stu-id="f0519-216">**Key**: environment</span></span>  
<span data-ttu-id="f0519-217">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-217">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-218">**Varsayılan**: üretim</span><span class="sxs-lookup"><span data-stu-id="f0519-218">**Default**: Production</span></span>  
<span data-ttu-id="f0519-219">Şunu **kullanarak ayarla**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="f0519-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="f0519-220">**Ortam değişkeni**:`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="f0519-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="f0519-221">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-221">The environment can be set to any value.</span></span> <span data-ttu-id="f0519-222">Çerçeve tanımlı değerler,, `Development` `Staging` ve içerir `Production` .</span><span class="sxs-lookup"><span data-stu-id="f0519-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="f0519-223">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="f0519-223">Values aren't case sensitive.</span></span> <span data-ttu-id="f0519-224">Varsayılan olarak *ortam* , `ASPNETCORE_ENVIRONMENT` ortam değişkeninden okunurdur.</span><span class="sxs-lookup"><span data-stu-id="f0519-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="f0519-225">[Visual Studio](https://visualstudio.microsoft.com)kullanırken, ortam değişkenleri dosyadaki *launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="f0519-226">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f0519-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="f0519-227">Başlangıç derlemelerini barındırma</span><span class="sxs-lookup"><span data-stu-id="f0519-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="f0519-228">Uygulamanın barındırma başlangıç derlemelerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f0519-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="f0519-229">**Anahtar**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="f0519-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="f0519-230">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-230">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-231">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="f0519-231">**Default**: Empty string</span></span>  
<span data-ttu-id="f0519-232">Şunu **kullanarak ayarla**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="f0519-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="f0519-233">**Ortam değişkeni**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="f0519-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="f0519-234">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="f0519-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="f0519-235">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f0519-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="f0519-236">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f0519-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="f0519-237">HTTPS bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="f0519-237">HTTPS Port</span></span>

<span data-ttu-id="f0519-238">HTTPS yeniden yönlendirme bağlantı noktasını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f0519-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="f0519-239">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0519-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f0519-240">**Anahtar**: https_port **türü**: *dize* 
 **varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="f0519-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="f0519-241">Şunu **kullanarak ayarla**: `UseSetting` 
 **ortam değişkeni**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="f0519-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="f0519-242">Barındırma başlatma derlemeleri dışlama</span><span class="sxs-lookup"><span data-stu-id="f0519-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="f0519-243">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="f0519-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="f0519-244">**Anahtar**: hostingstartupexcludeassemblies</span><span class="sxs-lookup"><span data-stu-id="f0519-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="f0519-245">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-245">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-246">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="f0519-246">**Default**: Empty string</span></span>  
<span data-ttu-id="f0519-247">Şunu **kullanarak ayarla**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="f0519-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="f0519-248">**Ortam değişkeni**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="f0519-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="f0519-249">Barındırma URL 'Lerini tercih et</span><span class="sxs-lookup"><span data-stu-id="f0519-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="f0519-250">Konağın `WebHostBuilder` uygulamayla yapılandırılanlar yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir `IServer` .</span><span class="sxs-lookup"><span data-stu-id="f0519-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="f0519-251">**Anahtar**: preferhostingurl 'leri</span><span class="sxs-lookup"><span data-stu-id="f0519-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="f0519-252">**Tür**: *bool* ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="f0519-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f0519-253">**Varsayılan**: true</span><span class="sxs-lookup"><span data-stu-id="f0519-253">**Default**: true</span></span>  
<span data-ttu-id="f0519-254">Şunu **kullanarak ayarla**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="f0519-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="f0519-255">**Ortam değişkeni**:`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="f0519-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="f0519-256">Barındırma başlangıcını engelle</span><span class="sxs-lookup"><span data-stu-id="f0519-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="f0519-257">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="f0519-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="f0519-258">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f0519-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="f0519-259">**Anahtar**: koruyucu thostingstartup</span><span class="sxs-lookup"><span data-stu-id="f0519-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="f0519-260">**Tür**: *bool* ( `true` veya `1` )</span><span class="sxs-lookup"><span data-stu-id="f0519-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f0519-261">**Varsayılan**: false</span><span class="sxs-lookup"><span data-stu-id="f0519-261">**Default**: false</span></span>  
<span data-ttu-id="f0519-262">Şunu **kullanarak ayarla**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="f0519-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="f0519-263">**Ortam değişkeni**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="f0519-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="f0519-264">Sunucu URL 'Leri</span><span class="sxs-lookup"><span data-stu-id="f0519-264">Server URLs</span></span>

<span data-ttu-id="f0519-265">Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokoller içeren IP adreslerini veya ana bilgisayar adreslerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f0519-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="f0519-266">**Anahtar**: URL 'ler</span><span class="sxs-lookup"><span data-stu-id="f0519-266">**Key**: urls</span></span>  
<span data-ttu-id="f0519-267">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-267">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-268">**Varsayılan**:http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="f0519-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="f0519-269">Şunu **kullanarak ayarla**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="f0519-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="f0519-270">**Ortam değişkeni**:`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="f0519-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="f0519-271">Noktalı virgülle ayrılmış olarak ayarlayın (;) sunucunun yanıtlaması gereken URL ön eklerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="f0519-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="f0519-272">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="f0519-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="f0519-273">\*Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin,) kullanarak herhangi BIR IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın `http://*:5000` .</span><span class="sxs-lookup"><span data-stu-id="f0519-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="f0519-274">Protokol ( `http://` veya `https://` ) her URL 'ye dahil edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f0519-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="f0519-275">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="f0519-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="f0519-276">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f0519-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="f0519-277">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f0519-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="f0519-278">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="f0519-278">Shutdown Timeout</span></span>

<span data-ttu-id="f0519-279">Web konağının kapanması için beklenecek süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="f0519-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="f0519-280">**Anahtar**: shutdowntimeoutseconds</span><span class="sxs-lookup"><span data-stu-id="f0519-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="f0519-281">**Tür**: *int*</span><span class="sxs-lookup"><span data-stu-id="f0519-281">**Type**: *int*</span></span>  
<span data-ttu-id="f0519-282">**Varsayılan**: 5</span><span class="sxs-lookup"><span data-stu-id="f0519-282">**Default**: 5</span></span>  
<span data-ttu-id="f0519-283">Şunu **kullanarak ayarla**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="f0519-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="f0519-284">**Ortam değişkeni**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="f0519-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="f0519-285">Anahtar, ile bir *int* kabul etse de `UseSetting` (örneğin, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` ), [useshutdowntimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) genişletme yöntemi bir [TimeSpan](/dotnet/api/system.timespan)alır.</span><span class="sxs-lookup"><span data-stu-id="f0519-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="f0519-286">Zaman aşımı süresi boyunca barındırma:</span><span class="sxs-lookup"><span data-stu-id="f0519-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="f0519-287">[Iapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)öğesini tetikler.</span><span class="sxs-lookup"><span data-stu-id="f0519-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="f0519-288">Üzerinde durmayacak hizmetlerin hatalarını günlüğe kaydetmek için barındırılan hizmetleri durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f0519-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="f0519-289">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="f0519-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="f0519-290">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="f0519-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="f0519-291">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="f0519-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="f0519-292">Başlangıç derlemesi</span><span class="sxs-lookup"><span data-stu-id="f0519-292">Startup Assembly</span></span>

<span data-ttu-id="f0519-293">Sınıfın aranacağı derlemeyi belirler `Startup` .</span><span class="sxs-lookup"><span data-stu-id="f0519-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="f0519-294">**Anahtar**: startupassembly</span><span class="sxs-lookup"><span data-stu-id="f0519-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="f0519-295">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-295">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-296">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="f0519-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="f0519-297">Şunu **kullanarak ayarla**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="f0519-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="f0519-298">**Ortam değişkeni**:`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="f0519-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="f0519-299">Ada ( `string` ) veya türe () göre derlemeye `TStartup` başvurulabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="f0519-300">Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="f0519-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="f0519-301">Web kökü</span><span class="sxs-lookup"><span data-stu-id="f0519-301">Web root</span></span>

<span data-ttu-id="f0519-302">Uygulamanın statik varlıklarının göreli yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f0519-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="f0519-303">**Anahtar**: Webroot</span><span class="sxs-lookup"><span data-stu-id="f0519-303">**Key**: webroot</span></span>  
<span data-ttu-id="f0519-304">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="f0519-304">**Type**: *string*</span></span>  
<span data-ttu-id="f0519-305">**Varsayılan**: varsayılan `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="f0519-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="f0519-306">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f0519-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="f0519-307">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0519-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="f0519-308">Şunu **kullanarak ayarla**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="f0519-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="f0519-309">**Ortam değişkeni**:`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="f0519-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="f0519-310">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f0519-310">For more information, see:</span></span>

* [<span data-ttu-id="f0519-311">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="f0519-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="f0519-312">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="f0519-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="f0519-313">Geçersiz kılma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f0519-313">Override configuration</span></span>

<span data-ttu-id="f0519-314">Web konağını yapılandırmak için [yapılandırma](xref:fundamentals/configuration/index) kullanın.</span><span class="sxs-lookup"><span data-stu-id="f0519-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="f0519-315">Aşağıdaki örnekte, konak yapılandırması isteğe bağlı olarak bir *hostsettings.js* dosyasında belirtilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="f0519-316">Dosyadaki *hostsettings.js* yüklenen herhangi bir yapılandırma, komut satırı bağımsız değişkenleri tarafından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="f0519-317">Oluşturulan yapılandırma (içinde `config` ), Konağı [useconfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)ile yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0519-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="f0519-318">`IWebHostBuilder`yapılandırma uygulamanın yapılandırmasına eklenir, ancak dönüştürme doğru değildir, &mdash; `ConfigureAppConfiguration` `IWebHostBuilder` yapılandırmayı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="f0519-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="f0519-319">Tarafından belirtilen yapılandırmayı `UseUrls` , ilk olarak yapılandırma *hostsettings.js* geçersiz kılma, komut satırı bağımsız değişkeni yapılandırma saniyesi:</span><span class="sxs-lookup"><span data-stu-id="f0519-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="f0519-320">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="f0519-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="f0519-321">[Useconfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) yalnızca belirtilen içindeki anahtarları `IConfiguration` ana bilgisayar Oluşturucu yapılandırmasına kopyalar.</span><span class="sxs-lookup"><span data-stu-id="f0519-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="f0519-322">Bu nedenle, `reloadOnChange: true` JSON, ını ve xml ayarları dosyalarının ayarlanması etkisizdir.</span><span class="sxs-lookup"><span data-stu-id="f0519-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="f0519-323">Belirli bir URL 'de çalıştırılacak Konağı belirtmek için, [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)yürütürken istenen değer bir komut isteminden geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="f0519-324">Komut satırı bağımsız değişkeni `urls` dosyadaki *hostsettings.js* değeri geçersiz kılar ve sunucu 8080 numaralı bağlantı noktasını dinler:</span><span class="sxs-lookup"><span data-stu-id="f0519-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="f0519-325">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="f0519-325">Manage the host</span></span>

<span data-ttu-id="f0519-326">**Çalıştır**</span><span class="sxs-lookup"><span data-stu-id="f0519-326">**Run**</span></span>

<span data-ttu-id="f0519-327">`Run`Yöntemi, Web uygulamasını başlatır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="f0519-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="f0519-328">**Başlangıç**</span><span class="sxs-lookup"><span data-stu-id="f0519-328">**Start**</span></span>

<span data-ttu-id="f0519-329">Yöntemini çağırarak, Konağı engellenmeyen bir şekilde çalıştırın `Start` :</span><span class="sxs-lookup"><span data-stu-id="f0519-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="f0519-330">Yöntemine bir URL listesi geçirilirse `Start` , belirtilen URL 'leri dinler:</span><span class="sxs-lookup"><span data-stu-id="f0519-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="f0519-331">Uygulama, `CreateDefaultBuilder` statik bir kolaylık yöntemi kullanarak önceden yapılandırılmış varsayılan ayarları kullanarak yeni bir ana bilgisayar başlatabilir ve başlatabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="f0519-332">Bu yöntemler, konsol çıktısı olmadan sunucuyu başlatır ve [Waitforkapatmadan](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) bir kesme (CTRL-C/sigint veya sigterim) bekler:</span><span class="sxs-lookup"><span data-stu-id="f0519-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="f0519-333">**Başlat (RequestDelegate uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="f0519-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="f0519-334">Kullanmaya başlayın `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="f0519-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="f0519-335">`http://localhost:5000`"Merhaba Dünya!" yanıtını almak için tarayıcıda bir istek yapın</span><span class="sxs-lookup"><span data-stu-id="f0519-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="f0519-336">`WaitForShutdown`kesme (CTRL-C/SIGINT veya SIGTERM) verilene kadar engeller.</span><span class="sxs-lookup"><span data-stu-id="f0519-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="f0519-337">Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş basışını, çıkış için bekler.</span><span class="sxs-lookup"><span data-stu-id="f0519-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="f0519-338">**Başlangıç (dize URL 'si, RequestDelegate uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="f0519-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="f0519-339">URL ile başlayın ve `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="f0519-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="f0519-340">Uygulamanın yanıt vermesi dışında, **Başlangıç (RequestDelegate uygulaması)** ile aynı sonucu üretir `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="f0519-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="f0519-341">**Başlat (eylem \<IRouteBuilder> routebuilder)**</span><span class="sxs-lookup"><span data-stu-id="f0519-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="f0519-342">`IRouteBuilder`Yönlendirme ara yazılımını kullanmak için ([Microsoft. aspnetcore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) örneğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f0519-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="f0519-343">Aşağıdaki tarayıcı isteklerini örnekle birlikte kullanın:</span><span class="sxs-lookup"><span data-stu-id="f0519-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="f0519-344">İstek</span><span class="sxs-lookup"><span data-stu-id="f0519-344">Request</span></span>                                    | <span data-ttu-id="f0519-345">Yanıt</span><span class="sxs-lookup"><span data-stu-id="f0519-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="f0519-346">Merhaba, martın!</span><span class="sxs-lookup"><span data-stu-id="f0519-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="f0519-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="f0519-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="f0519-348">"Ooops!" dizesiyle bir özel durum oluşturur</span><span class="sxs-lookup"><span data-stu-id="f0519-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="f0519-349">"Uh oh!" dizesiyle bir özel durum oluşturur</span><span class="sxs-lookup"><span data-stu-id="f0519-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="f0519-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="f0519-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="f0519-351">Merhaba Dünya!</span><span class="sxs-lookup"><span data-stu-id="f0519-351">Hello World!</span></span>                             |

<span data-ttu-id="f0519-352">`WaitForShutdown`kesme (CTRL-C/SIGINT veya SIGTERM) verilene kadar engeller.</span><span class="sxs-lookup"><span data-stu-id="f0519-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="f0519-353">Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş basışını, çıkış için bekler.</span><span class="sxs-lookup"><span data-stu-id="f0519-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="f0519-354">**Başlat (dize URL 'si, eylem \<IRouteBuilder> routebuilder)**</span><span class="sxs-lookup"><span data-stu-id="f0519-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="f0519-355">Bir URL ve örneği kullanın `IRouteBuilder` :</span><span class="sxs-lookup"><span data-stu-id="f0519-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="f0519-356">Uygulamanın yanıt vermesi dışında, **Başlangıç (eylem \<IRouteBuilder> routebuilder)** ile aynı sonucu üretir `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="f0519-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="f0519-357">**StartWith (eylem \<IApplicationBuilder> uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="f0519-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="f0519-358">Yapılandırmak için bir temsilci sağlayın `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="f0519-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="f0519-359">`http://localhost:5000`"Merhaba Dünya!" yanıtını almak için tarayıcıda bir istek yapın</span><span class="sxs-lookup"><span data-stu-id="f0519-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="f0519-360">`WaitForShutdown`kesme (CTRL-C/SIGINT veya SIGTERM) verilene kadar engeller.</span><span class="sxs-lookup"><span data-stu-id="f0519-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="f0519-361">Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş basışını, çıkış için bekler.</span><span class="sxs-lookup"><span data-stu-id="f0519-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="f0519-362">**StartWith (dize URL 'si, eylem \<IApplicationBuilder> uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="f0519-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="f0519-363">Yapılandırmak için bir URL ve temsilci sağlayın `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="f0519-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="f0519-364">Uygulamanın yanıt vermesi dışında, **StartWith (Action \<IApplicationBuilder> app) ile**aynı sonucu üretir `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="f0519-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="f0519-365">Iwebhostenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="f0519-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="f0519-366">`IWebHostEnvironment`Arabirim, uygulamanın Web barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f0519-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="f0519-367">Özelliklerini ve uzantı yöntemlerini kullanmak üzere sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın `IWebHostEnvironment` :</span><span class="sxs-lookup"><span data-stu-id="f0519-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="f0519-368">Uygulamayı ortama göre başlangıçta yapılandırmak için [kural tabanlı bir yaklaşım](xref:fundamentals/environments#environment-based-startup-class-and-methods) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="f0519-369">Alternatif olarak, `IWebHostEnvironment` `Startup` ' de kullanım için oluşturucuya ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f0519-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="f0519-370">Uzantı yöntemine ek olarak,, `IsDevelopment` `IWebHostEnvironment` `IsStaging` `IsProduction` ve `IsEnvironment(string environmentName)` yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="f0519-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="f0519-371">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f0519-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f0519-372">`IWebHostEnvironment`Hizmet ayrıca `Configure` işlem ardışık düzenini ayarlama yöntemine doğrudan eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="f0519-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="f0519-373">`IWebHostEnvironment``Invoke`özel [Ara yazılım](xref:fundamentals/middleware/write)oluşturulurken yöntemine eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="f0519-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="f0519-374">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="f0519-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="f0519-375">[Ihostingenvironment arabirimi](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) , uygulamanın Web barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f0519-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="f0519-376">Özelliklerini ve uzantı yöntemlerini kullanmak üzere sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın `IHostingEnvironment` :</span><span class="sxs-lookup"><span data-stu-id="f0519-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="f0519-377">Uygulamayı ortama göre başlangıçta yapılandırmak için [kural tabanlı bir yaklaşım](xref:fundamentals/environments#environment-based-startup-class-and-methods) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="f0519-378">Alternatif olarak, `IHostingEnvironment` `Startup` ' de kullanım için oluşturucuya ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f0519-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="f0519-379">Uzantı yöntemine ek olarak,, `IsDevelopment` `IHostingEnvironment` `IsStaging` `IsProduction` ve `IsEnvironment(string environmentName)` yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="f0519-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="f0519-380">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f0519-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f0519-381">`IHostingEnvironment`Hizmet ayrıca `Configure` işlem ardışık düzenini ayarlama yöntemine doğrudan eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="f0519-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="f0519-382">`IHostingEnvironment``Invoke`özel [Ara yazılım](xref:fundamentals/middleware/write)oluşturulurken yöntemine eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="f0519-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="f0519-383">Ihostapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="f0519-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="f0519-384">`IHostApplicationLifetime`başlatma sonrası ve kapalı etkinlikler için izin verir.</span><span class="sxs-lookup"><span data-stu-id="f0519-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="f0519-385">Arabirimdeki üç özellik, `Action` Başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="f0519-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="f0519-386">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="f0519-386">Cancellation Token</span></span>    | <span data-ttu-id="f0519-387">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="f0519-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="f0519-388">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="f0519-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="f0519-389">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="f0519-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="f0519-390">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f0519-390">All requests should be processed.</span></span> <span data-ttu-id="f0519-391">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="f0519-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="f0519-392">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="f0519-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="f0519-393">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-393">Requests may still be processing.</span></span> <span data-ttu-id="f0519-394">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="f0519-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="f0519-395">`StopApplication`uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="f0519-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="f0519-396">Aşağıdaki sınıf, `StopApplication` sınıfın yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır `Shutdown` :</span><span class="sxs-lookup"><span data-stu-id="f0519-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="f0519-397">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="f0519-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="f0519-398">[Iapplicationlifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) , başlatma sonrası ve kapalı etkinlikler için izin verir.</span><span class="sxs-lookup"><span data-stu-id="f0519-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="f0519-399">Arabirimdeki üç özellik, `Action` Başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="f0519-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="f0519-400">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="f0519-400">Cancellation Token</span></span>    | <span data-ttu-id="f0519-401">&#8230; tetiklendi</span><span class="sxs-lookup"><span data-stu-id="f0519-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="f0519-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="f0519-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="f0519-403">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="f0519-403">The host has fully started.</span></span> |
| [<span data-ttu-id="f0519-404">Applicationdurdurulan</span><span class="sxs-lookup"><span data-stu-id="f0519-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="f0519-405">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="f0519-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="f0519-406">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f0519-406">All requests should be processed.</span></span> <span data-ttu-id="f0519-407">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="f0519-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="f0519-408">Applicationdurduruluyor</span><span class="sxs-lookup"><span data-stu-id="f0519-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="f0519-409">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="f0519-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="f0519-410">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="f0519-410">Requests may still be processing.</span></span> <span data-ttu-id="f0519-411">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="f0519-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="f0519-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) , uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="f0519-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="f0519-413">Aşağıdaki sınıf, `StopApplication` sınıfın yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır `Shutdown` :</span><span class="sxs-lookup"><span data-stu-id="f0519-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f0519-414">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f0519-414">Scope validation</span></span>

<span data-ttu-id="f0519-415">[Createdefaultbuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) , uygulamanın ortamı geliştirmede [serviceprovideroptions. validatescopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) ' i ayarlar `true` .</span><span class="sxs-lookup"><span data-stu-id="f0519-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="f0519-416">, `ValidateScopes` Olarak ayarlandığında `true` , varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="f0519-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f0519-417">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="f0519-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f0519-418">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="f0519-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f0519-419">[Buildserviceprovider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) çağrıldığında kök hizmet sağlayıcısı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f0519-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="f0519-420">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f0519-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f0519-421">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="f0519-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f0519-422">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="f0519-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f0519-423">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="f0519-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f0519-424">Üretim ortamında da dahil olmak üzere kapsamları her zaman doğrulamak için, ana bilgisayar Oluşturucu 'da [Serviceprovideroptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) 'ı [usedefaultserviceprovider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) ile yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f0519-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="f0519-425">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f0519-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
