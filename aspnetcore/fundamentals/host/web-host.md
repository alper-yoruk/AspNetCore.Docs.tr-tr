---
title: ASP.NET Çekirdek Web Host
author: rick-anderson
description: Uygulama başlatma ve yaşam boyu yönetiminden sorumlu olan ASP.NET Core'daki Web Host hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666714"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="7a04d-103">ASP.NET Çekirdek Web Host</span><span class="sxs-lookup"><span data-stu-id="7a04d-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="7a04d-104">ASP.NET Core uygulamaları bir *ana bilgisayar*yapılandırır ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="7a04d-105">Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="7a04d-106">En azından, ana bilgisayar bir sunucu ve istek işleme ardışık yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="7a04d-107">Ana bilgisayar, günlüğe kaydetme, bağımlılık enjeksiyonu ve yapılandırma da ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a04d-108">Bu makale, yalnızca geriye dönük uyumluluk için kullanılabilir durumda olan Web Barındıran'ı kapsar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="7a04d-109">[Genel Ana Bilgisayar](xref:fundamentals/host/generic-host) tüm uygulama türleri için önerilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a04d-110">Bu makale, web uygulamalarını barındırmak için web barındırma için web barındırma alanı kapsar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="7a04d-111">Diğer uygulama türleri için [Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="7a04d-112">Ana bilgisayar ayarlama</span><span class="sxs-lookup"><span data-stu-id="7a04d-112">Set up a host</span></span>

<span data-ttu-id="7a04d-113">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)bir örnek kullanarak bir ana bilgisayar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7a04d-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="7a04d-114">Bu genellikle uygulamanın giriş noktasında, yöntemde `Main` gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="7a04d-115">Proje şablonlarında, `Main` *Program.cs*bulunur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="7a04d-116">Tipik bir uygulama, bir ana bilgisayar ayarlamaya başlamak için [CreateDefaultBuilder'ı](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) arar:</span><span class="sxs-lookup"><span data-stu-id="7a04d-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="7a04d-117">Çağıran `CreateDefaultBuilder` kod, oluşturucu nesneyi çağıran `CreateWebHostBuilder` `Main` `Run` koddan ayıran ,adlı bir yöntemdedir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="7a04d-118">[Varlık Framework Core araçlarını](/ef/core/miscellaneous/cli/)kullanıyorsanız bu ayırma gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="7a04d-119">Araçlar, uygulamayı çalıştırmadan ana bilgisayarı yapılandırmak için tasarım zamanında çağırabilecekleri bir `CreateWebHostBuilder` yöntem bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="7a04d-120">Bir alternatif uygulamaktır. `IDesignTimeDbContextFactory`</span><span class="sxs-lookup"><span data-stu-id="7a04d-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="7a04d-121">Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="7a04d-122">`CreateDefaultBuilder` aşağıdaki görevleri gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="7a04d-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="7a04d-123">Uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="7a04d-124">Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="7a04d-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="7a04d-125">İçerik [kökünü](xref:fundamentals/index#content-root) [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)tarafından döndürülen yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="7a04d-126">Yükler [ana bilgisayar yapılandırması:](#host-configuration-values)</span><span class="sxs-lookup"><span data-stu-id="7a04d-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="7a04d-127">Ortam değişkenleri önceden `ASPNETCORE_` belirlenmiş (örneğin, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="7a04d-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="7a04d-128">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="7a04d-128">Command-line arguments.</span></span>
* <span data-ttu-id="7a04d-129">Uygulama yapılandırmasını aşağıdaki sırayla yükler:</span><span class="sxs-lookup"><span data-stu-id="7a04d-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="7a04d-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7a04d-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="7a04d-131">*ayarları. {Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="7a04d-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="7a04d-132">[Uygulama](xref:security/app-secrets) giriş montajını `Development` kullanarak ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="7a04d-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="7a04d-133">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="7a04d-133">Environment variables.</span></span>
  * <span data-ttu-id="7a04d-134">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="7a04d-134">Command-line arguments.</span></span>
* <span data-ttu-id="7a04d-135">Konsol ve hata ayıklama çıkışı için [günlüğe kaydetmeyi](xref:fundamentals/logging/index) yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="7a04d-136">Günlüğe kaydetme, bir *appsettings.json* veya appsettings'in Günlük yapılandırma bölümünde belirtilen [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) kurallarını *içerir.{ Çevre}.json* dosyası.</span><span class="sxs-lookup"><span data-stu-id="7a04d-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="7a04d-137">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)ile IIS'nin arkasında koşarken, `CreateDefaultBuilder` uygulamanın temel adresini ve bağlantı noktasını yapılandıran [IIS Tümleştirmesi'ni](xref:host-and-deploy/iis/index)sağlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="7a04d-138">IIS Tümleştirme de [başlangıç hatalarını yakalamak](#capture-startup-errors)için uygulamayı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="7a04d-139">IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.</span><span class="sxs-lookup"><span data-stu-id="7a04d-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="7a04d-140">Uygulamanın ortamı Geliştirme `true` ise [ServiceProviderOptions.ValidateScopes'u](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="7a04d-141">Daha fazla bilgi için [Kapsam doğrulama'ya](#scope-validation)bakın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="7a04d-142">Tarafından `CreateDefaultBuilder` tanımlanan yapılandırma geçersiz kılınabilir ve [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)tarafından artırılabilir , [YapılandırmaLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), ve diğer yöntemler ve [iWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)uzantısı yöntemleri .</span><span class="sxs-lookup"><span data-stu-id="7a04d-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="7a04d-143">Birkaç örnek:</span><span class="sxs-lookup"><span data-stu-id="7a04d-143">A few examples follow:</span></span>

* <span data-ttu-id="7a04d-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) uygulama için `IConfiguration` ek belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="7a04d-145">Aşağıdaki `ConfigureAppConfiguration` *arama, appsettings.xml* dosyasına uygulama yapılandırmasını eklemek için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="7a04d-146">`ConfigureAppConfiguration`birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="7a04d-147">Bu yapılandırmanın ana bilgisayar için (örneğin, sunucu URL'leri veya ortam) geçerli olmadığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="7a04d-148">Ana [Bilgisayar yapılandırma değerleri](#host-configuration-values) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="7a04d-149">Aşağıdaki `ConfigureLogging` [çağrı, LogLevel.Warning'e](/dotnet/api/microsoft.extensions.logging.loglevel)minimum günlük düzeyini[(SetMinimumLevel)](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)yapılandırmak için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="7a04d-150">Bu ayar, uygulama ayarlarındaki ayarları geçersiz *kılar. Development.json* `LogLevel.Debug`( ) ve *uygulama ayarları. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`tarafından yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7a04d-151">`ConfigureLogging`birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7a04d-152">Kestrel `ConfigureKestrel` tarafından yapılandırıldığında kurulan 30.000.000 bayt varsayılan [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) geçersiz kılar `CreateDefaultBuilder`için aşağıdaki çağrı:</span><span class="sxs-lookup"><span data-stu-id="7a04d-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="7a04d-153">[UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) için aşağıdaki çağrı, Kestrel tarafından yapılandırıldığında kurulan 30.000.000 baytlık varsayılan [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) geçersiz `CreateDefaultBuilder`kılar:</span><span class="sxs-lookup"><span data-stu-id="7a04d-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="7a04d-154">[İçerik kökü,](xref:fundamentals/index#content-root) ana bilgisayarın MVC görünüm dosyaları gibi içerik dosyalarını nerede aramasını belirler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="7a04d-155">Uygulama projenin kök klasöründen başlatıldığında, projenin kök klasörü içerik kökü olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="7a04d-156">Bu, [Visual Studio](https://visualstudio.microsoft.com) ve [dotnet yeni şablonlarında](/dotnet/core/tools/dotnet-new)kullanılan varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="7a04d-157">Uygulama yapılandırması hakkında daha <xref:fundamentals/configuration/index>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="7a04d-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="7a04d-158">Statik `CreateDefaultBuilder` yöntemi kullanarak bir alternatif olarak, [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) bir ana bilgisayar oluşturma core 2.x ASP.NET ile desteklenen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="7a04d-159">Bir ana bilgisayar ayarlarken, Hizmetleri [Yapılandır](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) ve [Yapılandırışla](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) työntem sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="7a04d-160">Bir `Startup` sınıf belirtilirse, bir `Configure` yöntem tanımlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="7a04d-161">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7a04d-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="7a04d-162">Birbiriniz `ConfigureServices` için birden fazla çağrı.</span><span class="sxs-lookup"><span data-stu-id="7a04d-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="7a04d-163">Önceki ayarları `Configure` `UseStartup` n `WebHostBuilder` için veya değiştirme degistirme degistirme degistirme degistirme.</span><span class="sxs-lookup"><span data-stu-id="7a04d-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="7a04d-164">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="7a04d-164">Host configuration values</span></span>

<span data-ttu-id="7a04d-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ana bilgisayar yapılandırma değerlerini ayarlamak için aşağıdaki yaklaşımlara dayanır:</span><span class="sxs-lookup"><span data-stu-id="7a04d-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="7a04d-166">Biçimi ile ortam değişkenleri içeren ana `ASPNETCORE_{configurationKey}`bilgisayar oluşturucu yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="7a04d-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="7a04d-167">Örneğin, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="7a04d-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="7a04d-168">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) ve [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) gibi uzantılar [(Override yapılandırma](#override-configuration) bölümüne bakın).</span><span class="sxs-lookup"><span data-stu-id="7a04d-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="7a04d-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) ve ilişkili anahtar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="7a04d-170">Bir değer ayarı `UseSetting`yaparken, değer türüne bakılmaksızın bir dize olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="7a04d-171">Ana bilgisayar, değeri son olarak ayarlayan seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="7a04d-172">Daha fazla bilgi için, sonraki bölümde [Geçersiz Kılma yapılandırması](#override-configuration) bakın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="7a04d-173">Uygulama Anahtarı (Ad)</span><span class="sxs-lookup"><span data-stu-id="7a04d-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a04d-174">Ev `IWebHostEnvironment.ApplicationName` sahibi inşaatı sırasında [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) veya [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) çağrıldığında özellik otomatik olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="7a04d-175">Değer, uygulamanın giriş noktasını içeren derlemenin adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="7a04d-176">Değeri açıkça ayarlamak için [WebHostDefaults.ApplicationKey'i](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a04d-177">[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) [özelliği, Ev](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) sahibi inşaatı sırasında UseStartup veya [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) çağrıldığında otomatik olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="7a04d-178">Değer, uygulamanın giriş noktasını içeren derlemenin adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="7a04d-179">Değeri açıkça ayarlamak için [WebHostDefaults.ApplicationKey'i](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="7a04d-180">**Anahtar**: applicationName</span><span class="sxs-lookup"><span data-stu-id="7a04d-180">**Key**: applicationName</span></span>  
<span data-ttu-id="7a04d-181">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-181">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-182">**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="7a04d-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="7a04d-183">**Set kullanarak:**`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="7a04d-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="7a04d-184">**Çevre değişkeni**:`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="7a04d-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="7a04d-185">Başlangıç Hatalarını Yakalama</span><span class="sxs-lookup"><span data-stu-id="7a04d-185">Capture Startup Errors</span></span>

<span data-ttu-id="7a04d-186">Bu ayar, başlangıç hatalarının yakalanmasını denetler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="7a04d-187">**Anahtar**: yakalamaBaşlangıçHataları</span><span class="sxs-lookup"><span data-stu-id="7a04d-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="7a04d-188">**Türü**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="7a04d-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="7a04d-189">**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="7a04d-190">**Set kullanarak:**`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="7a04d-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="7a04d-191">**Çevre değişkeni**:`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="7a04d-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="7a04d-192">Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu.</span><span class="sxs-lookup"><span data-stu-id="7a04d-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="7a04d-193">Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.</span><span class="sxs-lookup"><span data-stu-id="7a04d-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="7a04d-194">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="7a04d-194">Content root</span></span>

<span data-ttu-id="7a04d-195">Bu ayar, ASP.NET Core'un içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="7a04d-196">**Anahtar**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="7a04d-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="7a04d-197">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-197">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-198">**Varsayılan**: Varsayılan olarak uygulama derlemesinin bulunduğu klasöre iner.</span><span class="sxs-lookup"><span data-stu-id="7a04d-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="7a04d-199">**Set kullanarak:**`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="7a04d-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="7a04d-200">**Çevre değişkeni**:`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="7a04d-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="7a04d-201">İçerik kökü de [web kökü](xref:fundamentals/index#web-root)için temel yol olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7a04d-202">İçerik kökü yolu yoksa, ana bilgisayar başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="7a04d-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="7a04d-203">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="7a04d-203">For more information, see:</span></span>

* [<span data-ttu-id="7a04d-204">Temel: İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="7a04d-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="7a04d-205">Web kökü</span><span class="sxs-lookup"><span data-stu-id="7a04d-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="7a04d-206">Ayrıntılı Hatalar</span><span class="sxs-lookup"><span data-stu-id="7a04d-206">Detailed Errors</span></span>

<span data-ttu-id="7a04d-207">Ayrıntılı hataların yakalanması gerektiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="7a04d-208">**Anahtar**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="7a04d-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="7a04d-209">**Türü**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="7a04d-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="7a04d-210">**Varsayılan**: false</span><span class="sxs-lookup"><span data-stu-id="7a04d-210">**Default**: false</span></span>  
<span data-ttu-id="7a04d-211">**Set kullanarak:**`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="7a04d-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="7a04d-212">**Çevre değişkeni**:`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="7a04d-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="7a04d-213">Etkinleştirildiğinde (veya <a href="#environment">Ortam</a> `Development`ayarlandığında), uygulama ayrıntılı özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="7a04d-214">Ortam</span><span class="sxs-lookup"><span data-stu-id="7a04d-214">Environment</span></span>

<span data-ttu-id="7a04d-215">Uygulamanın ortamını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-215">Sets the app's environment.</span></span>

<span data-ttu-id="7a04d-216">**Anahtar**: çevre</span><span class="sxs-lookup"><span data-stu-id="7a04d-216">**Key**: environment</span></span>  
<span data-ttu-id="7a04d-217">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-217">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-218">**Varsayılan**: Üretim</span><span class="sxs-lookup"><span data-stu-id="7a04d-218">**Default**: Production</span></span>  
<span data-ttu-id="7a04d-219">**Set kullanarak:**`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="7a04d-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="7a04d-220">**Çevre değişkeni**:`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="7a04d-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="7a04d-221">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-221">The environment can be set to any value.</span></span> <span data-ttu-id="7a04d-222">Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve .</span><span class="sxs-lookup"><span data-stu-id="7a04d-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="7a04d-223">Değerler büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-223">Values aren't case sensitive.</span></span> <span data-ttu-id="7a04d-224">Varsayılan olarak, *Çevre* çevre `ASPNETCORE_ENVIRONMENT` değişkeninden okunur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="7a04d-225">Visual [Studio](https://visualstudio.microsoft.com)kullanırken, ortam değişkenleri *launchSettings.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="7a04d-226">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a04d-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="7a04d-227">Hosting Başlangıç Meclisleri</span><span class="sxs-lookup"><span data-stu-id="7a04d-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="7a04d-228">Uygulamanın başlangıç derlemelerini barındırmasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="7a04d-229">**Anahtar**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="7a04d-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="7a04d-230">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-230">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-231">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="7a04d-231">**Default**: Empty string</span></span>  
<span data-ttu-id="7a04d-232">**Set kullanarak:**`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="7a04d-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="7a04d-233">**Çevre değişkeni**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7a04d-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="7a04d-234">Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="7a04d-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="7a04d-235">Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="7a04d-236">Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="7a04d-237">HTTPS Bağlantı Noktası</span><span class="sxs-lookup"><span data-stu-id="7a04d-237">HTTPS Port</span></span>

<span data-ttu-id="7a04d-238">HTTPS yönlendirme bağlantı noktasını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="7a04d-239">[HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7a04d-240">**Anahtar**: https_port **Türü**: *string*
**Default**: Varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="7a04d-241">**Set**using `UseSetting` 
: **Çevre değişkeni**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="7a04d-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="7a04d-242">Hosting Başlangıç Hariç Derlemeler</span><span class="sxs-lookup"><span data-stu-id="7a04d-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="7a04d-243">Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.</span><span class="sxs-lookup"><span data-stu-id="7a04d-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="7a04d-244">**Anahtar**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="7a04d-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="7a04d-245">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-245">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-246">**Varsayılan**: Boş dize</span><span class="sxs-lookup"><span data-stu-id="7a04d-246">**Default**: Empty string</span></span>  
<span data-ttu-id="7a04d-247">**Set kullanarak:**`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="7a04d-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="7a04d-248">**Çevre değişkeni**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7a04d-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="7a04d-249">Hosting URL'lerini tercih edin</span><span class="sxs-lookup"><span data-stu-id="7a04d-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="7a04d-250">Ana bilgisayar, `WebHostBuilder` `IServer` uygulamayla yapılandırılanlar yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="7a04d-251">**Anahtar**: tercihHostingUrls</span><span class="sxs-lookup"><span data-stu-id="7a04d-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="7a04d-252">**Türü**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="7a04d-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="7a04d-253">**Varsayılan**: true</span><span class="sxs-lookup"><span data-stu-id="7a04d-253">**Default**: true</span></span>  
<span data-ttu-id="7a04d-254">**Set kullanarak:**`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="7a04d-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="7a04d-255">**Çevre değişkeni**:`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="7a04d-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="7a04d-256">Hosting Başlangıç önleme</span><span class="sxs-lookup"><span data-stu-id="7a04d-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="7a04d-257">Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="7a04d-258">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7a04d-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="7a04d-259">**Anahtar**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="7a04d-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="7a04d-260">**Türü**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="7a04d-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="7a04d-261">**Varsayılan**: false</span><span class="sxs-lookup"><span data-stu-id="7a04d-261">**Default**: false</span></span>  
<span data-ttu-id="7a04d-262">**Set kullanarak:**`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="7a04d-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="7a04d-263">**Çevre değişkeni**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="7a04d-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="7a04d-264">Sunucu URL'leri</span><span class="sxs-lookup"><span data-stu-id="7a04d-264">Server URLs</span></span>

<span data-ttu-id="7a04d-265">Ip adreslerini veya ana bilgisayar adreslerini, sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokollerle gösterir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="7a04d-266">**Anahtar**: urls</span><span class="sxs-lookup"><span data-stu-id="7a04d-266">**Key**: urls</span></span>  
<span data-ttu-id="7a04d-267">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-267">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-268">**Varsayılan**:http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="7a04d-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="7a04d-269">**Set kullanarak:**`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="7a04d-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="7a04d-270">**Çevre değişkeni**:`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="7a04d-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="7a04d-271">Yarı kolon ayrılmış olarak ayarlayın (;) sunucunun yanıt vermesi gereken URL önekleri listesi.</span><span class="sxs-lookup"><span data-stu-id="7a04d-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="7a04d-272">Örneğin, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="7a04d-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="7a04d-273">Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="7a04d-274">Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="7a04d-275">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="7a04d-276">Kerkenez kendi bitiş noktası yapılandırma API vardır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="7a04d-277">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7a04d-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="7a04d-278">Kapatma Zaman</span><span class="sxs-lookup"><span data-stu-id="7a04d-278">Shutdown Timeout</span></span>

<span data-ttu-id="7a04d-279">Web Barındıran'ın kapanmasını beklemek için gereken süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="7a04d-280">**Anahtar**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="7a04d-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="7a04d-281">**Türü**: *int*</span><span class="sxs-lookup"><span data-stu-id="7a04d-281">**Type**: *int*</span></span>  
<span data-ttu-id="7a04d-282">**Varsayılan**: 5</span><span class="sxs-lookup"><span data-stu-id="7a04d-282">**Default**: 5</span></span>  
<span data-ttu-id="7a04d-283">**Set kullanarak:**`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="7a04d-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="7a04d-284">**Çevre değişkeni**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="7a04d-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="7a04d-285">Anahtar ile bir *int* `UseSetting` kabul etse `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`de (örneğin, ), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) uzantısı yöntemi bir [TimeSpan](/dotnet/api/system.timespan)alır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="7a04d-286">Zaman ekme döneminde, barındırma:</span><span class="sxs-lookup"><span data-stu-id="7a04d-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="7a04d-287">Tetikleyiciler [IApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="7a04d-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="7a04d-288">Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için hataları günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7a04d-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="7a04d-289">Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="7a04d-290">Hizmetler işleme meden ilerlememiş olsalar bile durur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="7a04d-291">Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="7a04d-292">Başlangıç Montajı</span><span class="sxs-lookup"><span data-stu-id="7a04d-292">Startup Assembly</span></span>

<span data-ttu-id="7a04d-293">Sınıfı aramak için derlemeyi `Startup` belirler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="7a04d-294">**Anahtar**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="7a04d-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="7a04d-295">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-295">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-296">**Varsayılan**: Uygulamanın montajı</span><span class="sxs-lookup"><span data-stu-id="7a04d-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="7a04d-297">**Set kullanarak:**`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="7a04d-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="7a04d-298">**Çevre değişkeni**:`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="7a04d-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="7a04d-299">Ada göre`string`derleme (`TStartup`) veya türü ( ) başvurulabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="7a04d-300">Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="7a04d-301">Web kökü</span><span class="sxs-lookup"><span data-stu-id="7a04d-301">Web root</span></span>

<span data-ttu-id="7a04d-302">Uygulamanın statik varlıklarına göreli yolu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="7a04d-303">**Anahtar**: webroot</span><span class="sxs-lookup"><span data-stu-id="7a04d-303">**Key**: webroot</span></span>  
<span data-ttu-id="7a04d-304">**Türü**: *string*</span><span class="sxs-lookup"><span data-stu-id="7a04d-304">**Type**: *string*</span></span>  
<span data-ttu-id="7a04d-305">**Varsayılan**: Varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="7a04d-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="7a04d-306">*{içerik kökü}/wwwroot* yolu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="7a04d-307">Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="7a04d-308">**Set kullanarak:**`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="7a04d-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="7a04d-309">**Çevre değişkeni**:`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="7a04d-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="7a04d-310">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="7a04d-310">For more information, see:</span></span>

* [<span data-ttu-id="7a04d-311">Temel: Web kökü</span><span class="sxs-lookup"><span data-stu-id="7a04d-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="7a04d-312">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="7a04d-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="7a04d-313">Yapılandırmayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="7a04d-313">Override configuration</span></span>

<span data-ttu-id="7a04d-314">Web Barındıran'ı yapılandırmak için [Yapılandırma'yı](xref:fundamentals/configuration/index) kullanın.</span><span class="sxs-lookup"><span data-stu-id="7a04d-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="7a04d-315">Aşağıdaki örnekte, ana bilgisayar yapılandırması isteğe bağlı olarak bir *hostsettings.json* dosyasında belirtilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="7a04d-316">*hostsettings.json* dosyasından yüklenen yapılandırma komut satırı bağımsız değişkenleri tarafından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="7a04d-317">Yapılandırılan yapılandırma `config`(in) barındıran bilgisayarı [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)ile yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a04d-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="7a04d-318">`IWebHostBuilder`yapılandırma uygulamanın yapılandırmasına eklenir, ancak tersi doğru&mdash; `ConfigureAppConfiguration` değildir `IWebHostBuilder` yapılandırmayı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="7a04d-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="7a04d-319">*Hostsettings.json* `UseUrls` config ilk, komut satırı config ikinci ile sağlanan yapılandırma geçersiz kılma:</span><span class="sxs-lookup"><span data-stu-id="7a04d-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="7a04d-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7a04d-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="7a04d-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) yalnızca sağlanan `IConfiguration` ana bilgisayar oluşturucu yapılandırmasından anahtarları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="7a04d-322">Bu nedenle, JSON, INI ve XML ayarları dosyaları için ayar `reloadOnChange: true` hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="7a04d-323">Belirli bir URL'de ana bilgisayar çalışmasını belirtmek için, [dotnet çalıştırırken](/dotnet/core/tools/dotnet-run)istenen değer komut isteminden geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="7a04d-324">Komut satırı bağımsız değişkeni `urls` *hostsettings.json* dosyasındaki değeri geçersiz kılar ve sunucu 8080 bağlantı noktasında dinler:</span><span class="sxs-lookup"><span data-stu-id="7a04d-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="7a04d-325">Ev sahibini yönetme</span><span class="sxs-lookup"><span data-stu-id="7a04d-325">Manage the host</span></span>

<span data-ttu-id="7a04d-326">**Çalıştırmak**</span><span class="sxs-lookup"><span data-stu-id="7a04d-326">**Run**</span></span>

<span data-ttu-id="7a04d-327">Yöntem `Run` web uygulamasını başlatır ve ana bilgisayar kapatAna kadar arama iş parçacığı engeller:</span><span class="sxs-lookup"><span data-stu-id="7a04d-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="7a04d-328">**Başlangıç**</span><span class="sxs-lookup"><span data-stu-id="7a04d-328">**Start**</span></span>

<span data-ttu-id="7a04d-329">Ana bilgisayarı, yöntemini çağırarak engelleyici `Start` olmayan bir şekilde çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="7a04d-330">URL'lerin bir listesi `Start` yönteme aktarılırsa, belirtilen URL'leri dinler:</span><span class="sxs-lookup"><span data-stu-id="7a04d-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="7a04d-331">Uygulama, statik bir kolaylık yöntemi `CreateDefaultBuilder` ni kullanarak önceden yapılandırılmış varsayılanları kullanarak yeni bir ana bilgisayar başlatabilir ve başlatabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="7a04d-332">Bu yöntemler konsol çıkışı olmadan sunucu başlatmak ve [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) bir mola (Ctrl-C/SIGINT veya SIGTERM) bekleyin:</span><span class="sxs-lookup"><span data-stu-id="7a04d-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="7a04d-333">**Başlat(RequestDelegate uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="7a04d-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="7a04d-334">A `RequestDelegate`ile başlayın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="7a04d-335">"Merhaba Dünya!" yanıtını `http://localhost:5000` almak için tarayıcıda bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="7a04d-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="7a04d-336">`WaitForShutdown`bir ara verilene kadar (Ctrl-C/SIGINT veya SIGTERM) bloke edilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="7a04d-337">Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş tuşuna basılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="7a04d-338">**Başlat(string url, RequestDelegate uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="7a04d-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="7a04d-339">URL ile başlayın `RequestDelegate`ve:</span><span class="sxs-lookup"><span data-stu-id="7a04d-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="7a04d-340">**Başlat(RequestDelegate uygulaması)** ile aynı sonucu üretir , `http://localhost:8080`ancak uygulama yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="7a04d-341">**Başlat (Eylem\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="7a04d-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="7a04d-342">Yönlendirme aracını `IRouteBuilder` kullanmak için[(Microsoft.AspNetCore.Routing)](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)örneğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="7a04d-343">Aşağıdaki tarayıcı isteklerini örnekle kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="7a04d-344">İstek</span><span class="sxs-lookup"><span data-stu-id="7a04d-344">Request</span></span>                                    | <span data-ttu-id="7a04d-345">Yanıt</span><span class="sxs-lookup"><span data-stu-id="7a04d-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="7a04d-346">Merhaba Martin!</span><span class="sxs-lookup"><span data-stu-id="7a04d-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="7a04d-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="7a04d-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="7a04d-348">Dize "ooops" ile bir istisna atar!</span><span class="sxs-lookup"><span data-stu-id="7a04d-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="7a04d-349">Dize "Uh oh!" ile bir istisna atar</span><span class="sxs-lookup"><span data-stu-id="7a04d-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="7a04d-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="7a04d-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="7a04d-351">Merhaba Dünya!</span><span class="sxs-lookup"><span data-stu-id="7a04d-351">Hello World!</span></span>                             |

<span data-ttu-id="7a04d-352">`WaitForShutdown`bir ara verilene kadar (Ctrl-C/SIGINT veya SIGTERM) bloke edilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="7a04d-353">Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş tuşuna basılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="7a04d-354">**Başlat(string url,\<Action IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="7a04d-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="7a04d-355">Bir URL ve bir `IRouteBuilder`örnek kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="7a04d-356">**Start (Action\<IRouteBuilder> routeBuilder)** ile aynı sonucu üretir, uygulama dışında `http://localhost:8080`yanıt verir .</span><span class="sxs-lookup"><span data-stu-id="7a04d-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="7a04d-357">**StartWith(Action\<IApplicationBuilder> uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="7a04d-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="7a04d-358">Bir yapılandırmak için `IApplicationBuilder`bir temsilci sağlayın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="7a04d-359">"Merhaba Dünya!" yanıtını `http://localhost:5000` almak için tarayıcıda bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="7a04d-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="7a04d-360">`WaitForShutdown`bir ara verilene kadar (Ctrl-C/SIGINT veya SIGTERM) bloke edilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="7a04d-361">Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş tuşuna basılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="7a04d-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="7a04d-362">**StartWith(string url,\<Action IApplicationBuilder> uygulaması)**</span><span class="sxs-lookup"><span data-stu-id="7a04d-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="7a04d-363">Bir url ve bir yapılandırılan `IApplicationBuilder`bir temsilci sağlayın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="7a04d-364">**StartWith (Action\<IApplicationBuilder> uygulaması)** ile aynı sonucu üretir `http://localhost:8080`, uygulama nın yanıt vermesi dışında.</span><span class="sxs-lookup"><span data-stu-id="7a04d-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="7a04d-365">IWebHostEnvironment arayüzü</span><span class="sxs-lookup"><span data-stu-id="7a04d-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="7a04d-366">Arayüz, `IWebHostEnvironment` uygulamanın web barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="7a04d-367">Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) `IWebHostEnvironment` kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="7a04d-368">Uygulamayı başlangıçta ortama göre yapılandırmak için [kural tabanlı](xref:fundamentals/environments#environment-based-startup-class-and-methods) bir yaklaşım kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="7a04d-369">Alternatif olarak, `IWebHostEnvironment` kullanmak `Startup` için yapıcı içine `ConfigureServices`enjekte:</span><span class="sxs-lookup"><span data-stu-id="7a04d-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="7a04d-370">Uzatma yöntemine `IsDevelopment` ek `IWebHostEnvironment` olarak, `IsProduction`teklifler `IsEnvironment(string environmentName)` `IsStaging`, , ve yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="7a04d-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="7a04d-371">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a04d-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7a04d-372">Hizmet, `IWebHostEnvironment` işleme ardışık `Configure` hattını ayarlama yöntemine doğrudan enjekte edilebilir:</span><span class="sxs-lookup"><span data-stu-id="7a04d-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="7a04d-373">`IWebHostEnvironment`özel [ara yazılım](xref:fundamentals/middleware/write) `Invoke` oluştururken yöntemiçine enjekte edilebilir:</span><span class="sxs-lookup"><span data-stu-id="7a04d-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="7a04d-374">IHostingEnvironment arayüzü</span><span class="sxs-lookup"><span data-stu-id="7a04d-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="7a04d-375">[IHostingEnvironment arayüzü](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) uygulamanın web barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="7a04d-376">Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) `IHostingEnvironment` kullanın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="7a04d-377">Uygulamayı başlangıçta ortama göre yapılandırmak için [kural tabanlı](xref:fundamentals/environments#environment-based-startup-class-and-methods) bir yaklaşım kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="7a04d-378">Alternatif olarak, `IHostingEnvironment` kullanmak `Startup` için yapıcı içine `ConfigureServices`enjekte:</span><span class="sxs-lookup"><span data-stu-id="7a04d-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="7a04d-379">Uzatma yöntemine `IsDevelopment` ek `IHostingEnvironment` olarak, `IsProduction`teklifler `IsEnvironment(string environmentName)` `IsStaging`, , ve yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="7a04d-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="7a04d-380">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a04d-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7a04d-381">Hizmet, `IHostingEnvironment` işleme ardışık `Configure` hattını ayarlama yöntemine doğrudan enjekte edilebilir:</span><span class="sxs-lookup"><span data-stu-id="7a04d-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="7a04d-382">`IHostingEnvironment`özel [ara yazılım](xref:fundamentals/middleware/write) `Invoke` oluştururken yöntemiçine enjekte edilebilir:</span><span class="sxs-lookup"><span data-stu-id="7a04d-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="7a04d-383">IHostApplicationÖmür arayüzü</span><span class="sxs-lookup"><span data-stu-id="7a04d-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="7a04d-384">`IHostApplicationLifetime`başlatma ve kapatma işlemlerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="7a04d-385">Arabirimdeki üç özellik, başlatma ve `Action` kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="7a04d-386">İptal Jetonu</span><span class="sxs-lookup"><span data-stu-id="7a04d-386">Cancellation Token</span></span>    | <span data-ttu-id="7a04d-387">&#8230; tetiklenir</span><span class="sxs-lookup"><span data-stu-id="7a04d-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="7a04d-388">Ev sahibi tamamen başladı.</span><span class="sxs-lookup"><span data-stu-id="7a04d-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="7a04d-389">Ev sahibi zarif bir kapatma tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="7a04d-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="7a04d-390">Tüm istekler işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-390">All requests should be processed.</span></span> <span data-ttu-id="7a04d-391">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="7a04d-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="7a04d-392">Ev sahibi zarif bir kapatma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="7a04d-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="7a04d-393">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-393">Requests may still be processing.</span></span> <span data-ttu-id="7a04d-394">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="7a04d-394">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="7a04d-395">`StopApplication`uygulamanın sonlandırılmasını talep ediyor.</span><span class="sxs-lookup"><span data-stu-id="7a04d-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="7a04d-396">Aşağıdaki sınıf, `StopApplication` sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="7a04d-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="7a04d-397">IApplicationLifetime arayüzü</span><span class="sxs-lookup"><span data-stu-id="7a04d-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="7a04d-398">[iApplicationLifetime,](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) başlangıç ve kapatma sonrası etkinliklere izin verir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="7a04d-399">Arabirimdeki üç özellik, başlatma ve `Action` kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="7a04d-400">İptal Jetonu</span><span class="sxs-lookup"><span data-stu-id="7a04d-400">Cancellation Token</span></span>    | <span data-ttu-id="7a04d-401">&#8230; tetiklenir</span><span class="sxs-lookup"><span data-stu-id="7a04d-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="7a04d-402">Başvurular Başladı</span><span class="sxs-lookup"><span data-stu-id="7a04d-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="7a04d-403">Ev sahibi tamamen başladı.</span><span class="sxs-lookup"><span data-stu-id="7a04d-403">The host has fully started.</span></span> |
| [<span data-ttu-id="7a04d-404">Başvurular Durduruldu</span><span class="sxs-lookup"><span data-stu-id="7a04d-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="7a04d-405">Ev sahibi zarif bir kapatma tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="7a04d-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="7a04d-406">Tüm istekler işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-406">All requests should be processed.</span></span> <span data-ttu-id="7a04d-407">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="7a04d-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="7a04d-408">Uygulama Durdurma</span><span class="sxs-lookup"><span data-stu-id="7a04d-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="7a04d-409">Ev sahibi zarif bir kapatma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="7a04d-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="7a04d-410">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-410">Requests may still be processing.</span></span> <span data-ttu-id="7a04d-411">Bu olay tamamlanana kadar kapatma blokları.</span><span class="sxs-lookup"><span data-stu-id="7a04d-411">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="7a04d-412">[StopApplication,](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="7a04d-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="7a04d-413">Aşağıdaki sınıf, `StopApplication` sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="7a04d-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="7a04d-414">Kapsam doğrulama</span><span class="sxs-lookup"><span data-stu-id="7a04d-414">Scope validation</span></span>

<span data-ttu-id="7a04d-415">[CreateDefaultBuilder,](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) uygulamanın ortamı Geliştirme `true` ise [ServiceProviderOptions.ValidateScopes'u](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="7a04d-416">Ne `ValidateScopes` zaman `true`ayarlanır , varsayılan hizmet sağlayıcısı bunu doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="7a04d-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7a04d-417">Kapsamlı hizmetler, kök hizmet sağlayıcısından doğrudan veya dolaylı olarak çözülmez.</span><span class="sxs-lookup"><span data-stu-id="7a04d-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="7a04d-418">Kapsamlı hizmetler doğrudan veya dolaylı olarak singleton içine enjekte değildir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="7a04d-419">[BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) çağrıldığında kök hizmet sağlayıcısı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7a04d-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="7a04d-420">Kök servis sağlayıcısının ömrü, sağlayıcı nın uygulamayla birlikte işe başlaması ve uygulama kapandığında imha edilmesiyle uygulamanın/sunucunun ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7a04d-421">Kapsamlı hizmetler, bunları oluşturan kapsayıcı tarafından bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7a04d-422">Kök kapsayıcıda kapsamlı bir hizmet oluşturulursa, yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından bertaraf edildiğinden, hizmetin ömrü etkin bir şekilde singleton'a yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="7a04d-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="7a04d-423">Hizmet kapsamlarını doğrulama çağrıldığında `BuildServiceProvider` bu durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="7a04d-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7a04d-424">Üretim ortamında da dahil olmak üzere kapsamları her zaman doğrulamak için, [ServiceProviderOptions'ı](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) ev sahibi oluşturucuda [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) ile yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="7a04d-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="7a04d-425">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7a04d-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
