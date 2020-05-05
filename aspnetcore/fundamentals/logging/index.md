---
title: .NET Core ve ASP.NET Core oturum açma
author: rick-anderson
description: Microsoft. Extensions. Logging NuGet paketi tarafından sunulan günlüğe kaydetme çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: ca62e374c6031ca3c2d438df87f2d13636d9c612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776109"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="22e38-103">.NET Core ve ASP.NET Core oturum açma</span><span class="sxs-lookup"><span data-stu-id="22e38-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22e38-104">[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="22e38-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="22e38-105">.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="22e38-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="22e38-106">Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="22e38-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="22e38-107">Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalardan oluşur.</span><span class="sxs-lookup"><span data-stu-id="22e38-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="22e38-108">Bu kod parçacıklarının günlüğe kaydetmeye özgü bölümleri, [genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanan tüm .NET Core uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="22e38-109">Genel konağın Web 'de olmayan bir uygulamada nasıl kullanılacağına ilişkin bir örnek için, [arka plan görevlerinin örnek uygulaması](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) *program.cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="22e38-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="22e38-110">Genel ana bilgisayarı olmayan uygulamalar için günlük kodu, [sağlayıcıların Eklenme](#add-providers) ve [günlükçülerin oluşturulma](#create-logs)biçiminde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="22e38-111">Ana bilgisayar olmayan kod örnekleri, makalenin bu bölümlerinde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="22e38-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22e38-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="22e38-113">Sağlayıcı Ekle</span><span class="sxs-lookup"><span data-stu-id="22e38-113">Add providers</span></span>

<span data-ttu-id="22e38-114">Günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="22e38-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="22e38-115">Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar.</span><span class="sxs-lookup"><span data-stu-id="22e38-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="22e38-116">Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="22e38-117">Genel ana bilgisayar kullanan bir uygulamaya sağlayıcı eklemek için, `Add{provider name}` *program.cs*' de sağlayıcının uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="22e38-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="22e38-118">Konak olmayan bir konsol uygulamasında, oluşturma sırasında sağlayıcının `Add{provider name}` uzantı yöntemini çağırın: `LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="22e38-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="22e38-119">`LoggerFactory`ve `AddConsole` için `Microsoft.Extensions.Logging`bir `using` ifade gerektirir.</span><span class="sxs-lookup"><span data-stu-id="22e38-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="22e38-120">Aşağıdaki günlük sağlayıcılarını ekleyen varsayılan ASP.NET Core <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>proje şablonları çağrısı:</span><span class="sxs-lookup"><span data-stu-id="22e38-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="22e38-121">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="22e38-122">H</span><span class="sxs-lookup"><span data-stu-id="22e38-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="22e38-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="22e38-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="22e38-124">[Olay günlüğü](#windows-eventlog-provider) (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="22e38-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="22e38-125">Varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="22e38-126">Öğesini <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>çağırın ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="22e38-127">Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="22e38-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="22e38-128">Günlükleri oluştur</span><span class="sxs-lookup"><span data-stu-id="22e38-128">Create logs</span></span>

<span data-ttu-id="22e38-129">Günlükler oluşturmak için bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesnesi kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="22e38-130">Bir Web uygulamasında veya barındırılan hizmette, bir `ILogger` bağımlılık ekleme (dı) kaynağından yararlanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="22e38-131">Konak dışı konsol uygulamalarında, `LoggerFactory` oluşturmak için öğesini kullanın. `ILogger`</span><span class="sxs-lookup"><span data-stu-id="22e38-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="22e38-132">Aşağıdaki ASP.NET Core örnek, kategorisi olarak içeren `TodoApiSample.Pages.AboutModel` bir günlükçü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="22e38-133">Günlük *kategorisi* , her günlük ile ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="22e38-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="22e38-134">Dı `ILogger<T>` tarafından belirtilen örnek, kategori `T` olarak tam nitelikli adı olan Günlükler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="22e38-135">Aşağıdaki konak olmayan konsol uygulaması örneği, kategorisi `LoggingConsoleApp.Program` olarak bir günlükçü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="22e38-136">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi olan `Information` Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="22e38-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="22e38-137">Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="22e38-138">[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="22e38-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="22e38-139">Program sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-139">Create logs in the Program class</span></span>

<span data-ttu-id="22e38-140">Günlükleri bir ASP.NET Core uygulamasının `Program` sınıfına yazmak için, konak oluşturulduktan sonra bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="22e38-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="22e38-141">Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="22e38-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="22e38-142">Ancak, ayrı bir günlükçü kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-142">However, a separate logger can be used.</span></span> <span data-ttu-id="22e38-143">Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="22e38-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="22e38-144">`AddSerilog`, içinde `Log.Logger`belirtilen statik yapılandırmayı kullanır:</span><span class="sxs-lookup"><span data-stu-id="22e38-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="22e38-145">Başlangıç sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-145">Create logs in the Startup class</span></span>

<span data-ttu-id="22e38-146">Günlükleri ASP.NET Core bir uygulama `Startup.Configure` yönteminde yazmak için, yöntem imzasına bir `ILogger` parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="22e38-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="22e38-147">`Startup.ConfigureServices` Yöntemde dı kapsayıcı kurulumu tamamlanmadan önce Günlüklerin yazılması desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="22e38-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="22e38-148">`Startup` Oluşturucuya günlükçü ekleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="22e38-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="22e38-149">`Startup.ConfigureServices` Yöntem imzasına günlükçü ekleme desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="22e38-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="22e38-150">Bu kısıtlamanın nedeni, günlük kaydının dı ve yapılandırmaya göre değişir ve bu da, ara ' ya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="22e38-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="22e38-151">Dı kapsayıcısı bitene kadar `ConfigureServices` ayarlanamaz.</span><span class="sxs-lookup"><span data-stu-id="22e38-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="22e38-152">Web ana bilgisayarı için ayrı bir `Startup` dı kapsayıcısı oluşturulduğundan, bir günlükçü 'nin önceki ASP.NET Core sürümlerinde çalışacak şekilde bir günlükçü ekleme.</span><span class="sxs-lookup"><span data-stu-id="22e38-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="22e38-153">Genel ana bilgisayar için yalnızca bir kapsayıcı oluşturma hakkında daha fazla bilgi için bkz. [Son değişiklik duyurusu](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="22e38-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="22e38-154">`ILogger<T>`' A bağımlı olan bir hizmeti yapılandırmanız gerekiyorsa, bunu Oluşturucu ekleme veya bir fabrika yöntemi sağlayarak de yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="22e38-155">Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="22e38-156">Örneğin, bir özelliği kaynağından bir hizmete doldurmanız gerektiğini varsayalım:</span><span class="sxs-lookup"><span data-stu-id="22e38-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="22e38-157">Önceki vurgulanmış kod, ilk kez `Func` dı kapsayıcısının bir örneğini oluşturmak için gereken bir ' dır `MyService`.</span><span class="sxs-lookup"><span data-stu-id="22e38-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="22e38-158">Kayıtlı hizmetlerden herhangi birine bu şekilde erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="22e38-159">Blazor 'te günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="22e38-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="22e38-160">Blazor WebAssembly</span></span>

<span data-ttu-id="22e38-161">Blazor WebAssembly uygulamalarında oturum açmayı, `WebAssemblyHostBuilder.Logging` içindeki `Program.Main`özelliği ile yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="22e38-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="22e38-162">`Logging` Özelliği <xref:Microsoft.Extensions.Logging.ILoggingBuilder>türündedir, bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde `Logging`de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="22e38-163">Razor bileşenlerinde oturum açma</span><span class="sxs-lookup"><span data-stu-id="22e38-163">Log in Razor components</span></span>

<span data-ttu-id="22e38-164">Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="22e38-165">İçin `using` <xref:Microsoft.Extensions.Logging> olan yönergesi, <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> ve <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>gibi API 'leri için IntelliSense 'i desteklemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="22e38-166">Aşağıdaki örnek, Razor bileşenleri <xref:Microsoft.Extensions.Logging.ILogger> içindeki günlüğü gösterir:</span><span class="sxs-lookup"><span data-stu-id="22e38-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="22e38-167">Aşağıdaki örnek, Razor bileşenleri <xref:Microsoft.Extensions.Logging.ILoggerFactory> içindeki günlüğü gösterir:</span><span class="sxs-lookup"><span data-stu-id="22e38-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="22e38-168">Zaman uyumsuz günlükçü yöntemi yok</span><span class="sxs-lookup"><span data-stu-id="22e38-168">No asynchronous logger methods</span></span>

<span data-ttu-id="22e38-169">Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="22e38-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="22e38-170">Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="22e38-171">Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="22e38-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="22e38-172">Örneğin, SQL Server için oturum açıyorsanız `Log` `Log` Yöntemler zaman uyumlu olduğundan, bunu doğrudan bir yöntemde yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="22e38-173">Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin.</span><span class="sxs-lookup"><span data-stu-id="22e38-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="22e38-174">Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="22e38-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="22e38-175">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="22e38-175">Configuration</span></span>

<span data-ttu-id="22e38-176">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="22e38-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="22e38-177">Dosya biçimleri (ıNı, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="22e38-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="22e38-178">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-178">Command-line arguments.</span></span>
* <span data-ttu-id="22e38-179">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-179">Environment variables.</span></span>
* <span data-ttu-id="22e38-180">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="22e38-181">Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.</span><span class="sxs-lookup"><span data-stu-id="22e38-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="22e38-182">[Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="22e38-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="22e38-183">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="22e38-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="22e38-184">Örneğin, günlük yapılandırma genellikle uygulama ayarları dosyalarının `Logging` bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="22e38-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="22e38-185">Aşağıdaki örnek tipik bir appSettings 'in içeriğini gösterir *. Development. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="22e38-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="22e38-186">Özelliği `Logging` , ve günlük `LogLevel` sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="22e38-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="22e38-187">Altındaki `LogLevel` `Logging` özelliği, Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="22e38-188">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe kaydedilir ve diğer tüm diğerleri `Debug` düzeyinde günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="22e38-189">Günlük sağlayıcılarını belirt `Logging` altındaki diğer özellikler.</span><span class="sxs-lookup"><span data-stu-id="22e38-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="22e38-190">Örnek, konsol sağlayıcısına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="22e38-190">The example is for the Console provider.</span></span> <span data-ttu-id="22e38-191">Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa, `IncludeScopes` etkinleştirilip etkinleştirilmeyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="22e38-192">Bir sağlayıcı özelliği (örnekte olduğu `Console` gibi), bir `LogLevel` özellik de belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="22e38-193">`LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="22e38-194">' De `Logging.{providername}.LogLevel`düzeyler belirtilirse, içinde `Logging.LogLevel`ayarlanan her şeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="22e38-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="22e38-195">Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez.</span><span class="sxs-lookup"><span data-stu-id="22e38-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="22e38-196">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="22e38-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="22e38-197">Örneğin, ayar dosyalarını okumak için tarafından `CreateDefaultBuilder` eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), günlük yapılandırmasını varsayılan olarak yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="22e38-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="22e38-198">Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="22e38-199">Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için <xref:fundamentals/configuration/index>bkz..</span><span class="sxs-lookup"><span data-stu-id="22e38-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="22e38-200">Örnek günlüğe kaydetme çıkışı</span><span class="sxs-lookup"><span data-stu-id="22e38-200">Sample logging output</span></span>

<span data-ttu-id="22e38-201">Yukarıdaki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında Günlükler konsolunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="22e38-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="22e38-202">Konsol çıkışının bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22e38-202">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="22e38-203">Yukarıdaki Günlükler, üzerinde `http://localhost:5000/api/todo/0`örnek uygulamaya bir http get isteği yapılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="22e38-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="22e38-204">Visual Studio 'da örnek uygulamayı çalıştırdığınızda hata ayıklama penceresinde göründükleri günlüklere yönelik bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22e38-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="22e38-205">Yukarıdaki bölümde gösterilen `ILogger` çağrılar tarafından oluşturulan Günlükler "TodoApiSample" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="22e38-206">"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır.</span><span class="sxs-lookup"><span data-stu-id="22e38-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="22e38-207">ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="22e38-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="22e38-208">Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="22e38-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="22e38-209">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="22e38-209">NuGet packages</span></span>

<span data-ttu-id="22e38-210">`ILogger` Ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde ve bu uygulamalar için varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="22e38-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="22e38-211">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="22e38-211">Log category</span></span>

<span data-ttu-id="22e38-212">Bir `ILogger` nesne oluşturulduğunda, için bir *Kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="22e38-213">Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="22e38-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="22e38-214">Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="22e38-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="22e38-215">Kategori `ILogger<T>` `T` olarak tam nitelikli `ILogger` tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="22e38-216">Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="22e38-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="22e38-217">`ILogger<T>`, tam nitelikli tür `CreateLogger` adı ile çağırma ile eşdeğerdir `T`.</span><span class="sxs-lookup"><span data-stu-id="22e38-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="22e38-218">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="22e38-218">Log level</span></span>

<span data-ttu-id="22e38-219">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="22e38-220">Günlük düzeyi önem derecesini veya önemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="22e38-221">Örneğin, bir yöntem normal olarak sona `Information` erdiğinde ve `Warning` bir yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="22e38-222">Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:</span><span class="sxs-lookup"><span data-stu-id="22e38-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="22e38-223">Yukarıdaki kodda, ilk parametre [günlük olay kimliğidir](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="22e38-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="22e38-224">İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="22e38-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="22e38-225">Yöntem parametreleri bu makalenin ilerleyen kısımlarında bulunan [ileti şablonu bölümünde](#log-message-template) açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="22e38-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="22e38-226">Yöntem adında düzeyi (örneğin, `LogInformation` ve `LogWarning`) Içeren günlük yöntemleri, [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="22e38-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="22e38-227">Bu yöntemler bir `LogLevel` parametre `Log` alan bir yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="22e38-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="22e38-228">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak söz dizimi görece karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="22e38-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="22e38-229">Daha fazla bilgi için bkz <xref:Microsoft.Extensions.Logging.ILogger> . ve [günlükçü uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="22e38-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="22e38-230">ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="22e38-231">İzleme = 0</span><span class="sxs-lookup"><span data-stu-id="22e38-231">Trace = 0</span></span>

  <span data-ttu-id="22e38-232">Genellikle yalnızca hata ayıklama için değerli bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="22e38-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="22e38-233">Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="22e38-234">*Varsayılan olarak devre dışıdır.*</span><span class="sxs-lookup"><span data-stu-id="22e38-234">*Disabled by default.*</span></span>

* <span data-ttu-id="22e38-235">Hata Ayıkla = 1</span><span class="sxs-lookup"><span data-stu-id="22e38-235">Debug = 1</span></span>

  <span data-ttu-id="22e38-236">Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="22e38-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="22e38-237">Örnek: `Entering method Configure with flag set to true.` yüksek `Debug` günlük hacimden dolayı yalnızca sorun giderirken üretim ortamında düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="22e38-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="22e38-238">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="22e38-238">Information = 2</span></span>

  <span data-ttu-id="22e38-239">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="22e38-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="22e38-240">Bu günlüklerde genellikle uzun süreli bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="22e38-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="22e38-241">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="22e38-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="22e38-242">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="22e38-242">Warning = 3</span></span>

  <span data-ttu-id="22e38-243">Uygulama akışında anormal veya beklenmedik olaylar için.</span><span class="sxs-lookup"><span data-stu-id="22e38-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="22e38-244">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="22e38-245">İşlenmiş özel durumlar, `Warning` günlük düzeyini kullanmak için yaygın bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="22e38-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="22e38-246">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="22e38-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="22e38-247">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="22e38-247">Error = 4</span></span>

  <span data-ttu-id="22e38-248">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="22e38-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="22e38-249">Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="22e38-250">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="22e38-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="22e38-251">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="22e38-251">Critical = 5</span></span>

  <span data-ttu-id="22e38-252">Anında ilgilenilmesi gereken hatalarda.</span><span class="sxs-lookup"><span data-stu-id="22e38-252">For failures that require immediate attention.</span></span> <span data-ttu-id="22e38-253">Örnekler: veri kaybı senaryoları, disk alanı yetersiz.</span><span class="sxs-lookup"><span data-stu-id="22e38-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="22e38-254">Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="22e38-255">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="22e38-255">For example:</span></span>

* <span data-ttu-id="22e38-256">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="22e38-256">In production:</span></span>
  * <span data-ttu-id="22e38-257">Geçiş `Information` düzeyinde günlüğe `Trace` kaydetme, yüksek hacimli ayrıntılı günlük iletileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="22e38-258">Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, düzey `Trace` iletiler `Information` üzerinden yüksek hacimli, düşük maliyetli bir veri deposuna oturum açın.</span><span class="sxs-lookup"><span data-stu-id="22e38-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="22e38-259">`Warning` Düzeylerde oturum açma işlemi genellikle daha az, daha küçük günlük iletileri `Critical` üretir.</span><span class="sxs-lookup"><span data-stu-id="22e38-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="22e38-260">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.</span><span class="sxs-lookup"><span data-stu-id="22e38-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="22e38-261">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="22e38-261">During development:</span></span>
  * <span data-ttu-id="22e38-262">Konsola `Warning` iletiler `Critical` aracılığıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="22e38-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="22e38-263">Sorun `Trace` giderirken `Information` iletiler aracılığıyla ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="22e38-264">Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="22e38-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="22e38-265">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="22e38-266">Bu makalenin önceki kısımlarında yer alınan günlük örnekleri, günlüklerin `Information` altında tutulur, bu `Debug` nedenle `Trace` hiçbir veya düzeyi günlük oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="22e38-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="22e38-267">Günlükleri göstermek `Debug` için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22e38-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="22e38-268">Günlüğe olay KIMLIĞI</span><span class="sxs-lookup"><span data-stu-id="22e38-268">Log event ID</span></span>

<span data-ttu-id="22e38-269">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="22e38-270">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="22e38-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="22e38-271">Olay KIMLIĞI bir olay kümesini ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="22e38-271">An event ID associates a set of events.</span></span> <span data-ttu-id="22e38-272">Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="22e38-273">Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="22e38-274">Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor.</span><span class="sxs-lookup"><span data-stu-id="22e38-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="22e38-275">Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:</span><span class="sxs-lookup"><span data-stu-id="22e38-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="22e38-276">Günlük iletisi şablonu</span><span class="sxs-lookup"><span data-stu-id="22e38-276">Log message template</span></span>

<span data-ttu-id="22e38-277">Her günlük bir ileti şablonunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-277">Each log specifies a message template.</span></span> <span data-ttu-id="22e38-278">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="22e38-279">Sayılar değil, yer tutucular için adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="22e38-280">Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası.</span><span class="sxs-lookup"><span data-stu-id="22e38-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="22e38-281">Aşağıdaki kodda, parametre adlarının ileti şablonunda sıra dışı olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="22e38-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="22e38-282">Bu kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="22e38-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="22e38-283">Günlüğe kaydetme altyapısı bu şekilde çalışarak, günlük sağlayıcılarının [yapılandırılmış günlüğe yazma olarak da bilinen anlamsal günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulayabilmesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="22e38-284">Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="22e38-285">Bu bilgiler, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="22e38-286">Örneğin, günlükçü yönteminin şuna benzer şekilde göründüğünü varsayın:</span><span class="sxs-lookup"><span data-stu-id="22e38-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="22e38-287">Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı, günlük verilerinde sorguları basitleştiren `ID` ve `RequestTime` özelliklere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="22e38-288">Bir sorgu, belirli `RequestTime` bir aralıktaki tüm günlükleri, kısa mesajdan zaman aşımını ayrıştırmadan bulabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="22e38-289">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="22e38-289">Logging exceptions</span></span>

<span data-ttu-id="22e38-290">Günlükçü yöntemlerinin, aşağıdaki örnekte olduğu gibi bir özel durum iletmenizi sağlayan aşırı yüklemeleri vardır:</span><span class="sxs-lookup"><span data-stu-id="22e38-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="22e38-291">Özel durum bilgileri farklı yollarla farklı sağlayıcılarda işler.</span><span class="sxs-lookup"><span data-stu-id="22e38-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="22e38-292">Yukarıda gösterilen koddan hata ayıklama sağlayıcısı çıktısına bir örnek aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="22e38-293">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="22e38-293">Log filtering</span></span>

<span data-ttu-id="22e38-294">Belirli bir sağlayıcı ve kategori için en az bir günlük düzeyi veya tüm sağlayıcılar ya da tüm kategoriler için belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="22e38-295">Minimum düzeyin altındaki tüm Günlükler bu sağlayıcıya aktarılmaz, bu nedenle görüntülenmez veya depolanmaz.</span><span class="sxs-lookup"><span data-stu-id="22e38-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="22e38-296">Tüm günlükleri gizlemek için en düşük `LogLevel.None` günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="22e38-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="22e38-297">Öğesinin `LogLevel.None` tamsayı değeri 6 ' dır `LogLevel.Critical` (5) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="22e38-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="22e38-298">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-298">Create filter rules in configuration</span></span>

<span data-ttu-id="22e38-299">Proje şablonu kodu, konsol `CreateDefaultBuilder` , hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="22e38-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="22e38-300">`CreateDefaultBuilder` Yöntemi, [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, bir `Logging` bölümde yapılandırmayı aramak için günlük kaydı yapar.</span><span class="sxs-lookup"><span data-stu-id="22e38-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="22e38-301">Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="22e38-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="22e38-302">Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="22e38-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="22e38-303">Bir `ILogger` nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="22e38-304">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="22e38-304">Filter rules in code</span></span>

<span data-ttu-id="22e38-305">Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="22e38-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="22e38-306">İkincisi `AddFilter` , hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="22e38-307">Birincisi `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="22e38-308">Filtreleme kuralları nasıl uygulanır</span><span class="sxs-lookup"><span data-stu-id="22e38-308">How filtering rules are applied</span></span>

<span data-ttu-id="22e38-309">Yapılandırma verileri ve önceki örneklerde `AddFilter` gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="22e38-310">İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.</span><span class="sxs-lookup"><span data-stu-id="22e38-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="22e38-311">Sayı</span><span class="sxs-lookup"><span data-stu-id="22e38-311">Number</span></span> | <span data-ttu-id="22e38-312">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="22e38-312">Provider</span></span>      | <span data-ttu-id="22e38-313">Şununla başlayan Kategoriler...</span><span class="sxs-lookup"><span data-stu-id="22e38-313">Categories that begin with ...</span></span>          | <span data-ttu-id="22e38-314">En düşük günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="22e38-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="22e38-315">1</span><span class="sxs-lookup"><span data-stu-id="22e38-315">1</span></span>      | <span data-ttu-id="22e38-316">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-316">Debug</span></span>         | <span data-ttu-id="22e38-317">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="22e38-317">All categories</span></span>                          | <span data-ttu-id="22e38-318">Bilgi</span><span class="sxs-lookup"><span data-stu-id="22e38-318">Information</span></span>       |
| <span data-ttu-id="22e38-319">2</span><span class="sxs-lookup"><span data-stu-id="22e38-319">2</span></span>      | <span data-ttu-id="22e38-320">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-320">Console</span></span>       | <span data-ttu-id="22e38-321">Microsoft. AspNetCore. Mvc. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="22e38-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="22e38-322">Uyarı</span><span class="sxs-lookup"><span data-stu-id="22e38-322">Warning</span></span>           |
| <span data-ttu-id="22e38-323">3</span><span class="sxs-lookup"><span data-stu-id="22e38-323">3</span></span>      | <span data-ttu-id="22e38-324">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-324">Console</span></span>       | <span data-ttu-id="22e38-325">Microsoft. AspNetCore. Mvc. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="22e38-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="22e38-326">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-326">Debug</span></span>             |
| <span data-ttu-id="22e38-327">4</span><span class="sxs-lookup"><span data-stu-id="22e38-327">4</span></span>      | <span data-ttu-id="22e38-328">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-328">Console</span></span>       | <span data-ttu-id="22e38-329">Microsoft. AspNetCore. Mvc. Razor</span><span class="sxs-lookup"><span data-stu-id="22e38-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="22e38-330">Hata</span><span class="sxs-lookup"><span data-stu-id="22e38-330">Error</span></span>             |
| <span data-ttu-id="22e38-331">5</span><span class="sxs-lookup"><span data-stu-id="22e38-331">5</span></span>      | <span data-ttu-id="22e38-332">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-332">Console</span></span>       | <span data-ttu-id="22e38-333">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="22e38-333">All categories</span></span>                          | <span data-ttu-id="22e38-334">Bilgi</span><span class="sxs-lookup"><span data-stu-id="22e38-334">Information</span></span>       |
| <span data-ttu-id="22e38-335">6</span><span class="sxs-lookup"><span data-stu-id="22e38-335">6</span></span>      | <span data-ttu-id="22e38-336">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="22e38-336">All providers</span></span> | <span data-ttu-id="22e38-337">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="22e38-337">All categories</span></span>                          | <span data-ttu-id="22e38-338">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-338">Debug</span></span>             |
| <span data-ttu-id="22e38-339">7</span><span class="sxs-lookup"><span data-stu-id="22e38-339">7</span></span>      | <span data-ttu-id="22e38-340">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="22e38-340">All providers</span></span> | <span data-ttu-id="22e38-341">Sistem</span><span class="sxs-lookup"><span data-stu-id="22e38-341">System</span></span>                                  | <span data-ttu-id="22e38-342">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-342">Debug</span></span>             |
| <span data-ttu-id="22e38-343">8</span><span class="sxs-lookup"><span data-stu-id="22e38-343">8</span></span>      | <span data-ttu-id="22e38-344">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-344">Debug</span></span>         | <span data-ttu-id="22e38-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="22e38-345">Microsoft</span></span>                               | <span data-ttu-id="22e38-346">İzleme</span><span class="sxs-lookup"><span data-stu-id="22e38-346">Trace</span></span>             |

<span data-ttu-id="22e38-347">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="22e38-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="22e38-348">Bir `ILogger` örnek tarafından yazılan tüm iletiler, seçilen kurallara göre filtrelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="22e38-349">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="22e38-350">Belirli bir kategori için oluşturulan her sağlayıcı `ILogger` için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="22e38-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="22e38-351">Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="22e38-352">Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="22e38-353">Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="22e38-354">Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="22e38-355">Birden çok kural seçilirse, **son** olanı götürün.</span><span class="sxs-lookup"><span data-stu-id="22e38-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="22e38-356">Hiçbir kural seçilmezse, kullanın `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="22e38-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="22e38-357">Yukarıdaki kurallar listesinde, "Microsoft. AspNetCore. Mvc `ILogger` . Razor. RazorViewEngine" kategorisi için bir nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="22e38-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="22e38-358">Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="22e38-359">Kural 8 ' i en özeldir, yani seçili olanı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="22e38-360">Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="22e38-361">Kural 3 en özeldir.</span><span class="sxs-lookup"><span data-stu-id="22e38-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="22e38-362">Elde edilen `ILogger` örnek, hata ayıklama `Trace` sağlayıcısına düzeyi ve üzeri Günlükler gönderir.</span><span class="sxs-lookup"><span data-stu-id="22e38-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="22e38-363">`Debug` Düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="22e38-364">Sağlayıcı diğer adları</span><span class="sxs-lookup"><span data-stu-id="22e38-364">Provider aliases</span></span>

<span data-ttu-id="22e38-365">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="22e38-366">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="22e38-367">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-367">Console</span></span>
* <span data-ttu-id="22e38-368">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-368">Debug</span></span>
* <span data-ttu-id="22e38-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="22e38-369">EventSource</span></span>
* <span data-ttu-id="22e38-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="22e38-370">EventLog</span></span>
* <span data-ttu-id="22e38-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="22e38-371">TraceSource</span></span>
* <span data-ttu-id="22e38-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="22e38-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="22e38-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="22e38-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="22e38-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="22e38-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="22e38-375">Varsayılan en düşük düzey</span><span class="sxs-lookup"><span data-stu-id="22e38-375">Default minimum level</span></span>

<span data-ttu-id="22e38-376">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kural uygulanmaz geçerli olan en düşük düzey ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="22e38-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="22e38-377">Aşağıdaki örnekte, en düşük düzeyin nasıl ayarlanacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="22e38-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="22e38-378">En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer olur `Information`ve `Trace` `Debug` bu, günlüklerin yok sayılacağı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="22e38-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="22e38-379">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="22e38-379">Filter functions</span></span>

<span data-ttu-id="22e38-380">Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="22e38-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="22e38-381">İşlevindeki kodun sağlayıcı türü, kategorisi ve günlük düzeyine erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="22e38-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="22e38-382">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="22e38-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="22e38-383">Sistem kategorileri ve Düzeyler</span><span class="sxs-lookup"><span data-stu-id="22e38-383">System categories and levels</span></span>

<span data-ttu-id="22e38-384">ASP.NET Core ve Entity Framework Core tarafından kullanılan bazı kategoriler şunlardır ve bunlardan beklenen Günlükler hakkında notlar bulunur:</span><span class="sxs-lookup"><span data-stu-id="22e38-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="22e38-385">Kategori</span><span class="sxs-lookup"><span data-stu-id="22e38-385">Category</span></span>                            | <span data-ttu-id="22e38-386">Notlar</span><span class="sxs-lookup"><span data-stu-id="22e38-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="22e38-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="22e38-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="22e38-388">Genel ASP.NET Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="22e38-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="22e38-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="22e38-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="22e38-390">Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı.</span><span class="sxs-lookup"><span data-stu-id="22e38-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="22e38-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="22e38-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="22e38-392">İzin verilen konaklar.</span><span class="sxs-lookup"><span data-stu-id="22e38-392">Hosts allowed.</span></span> |
| <span data-ttu-id="22e38-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="22e38-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="22e38-394">HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları.</span><span class="sxs-lookup"><span data-stu-id="22e38-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="22e38-395">Hangi barındırma başlangıç derlemeleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="22e38-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="22e38-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="22e38-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="22e38-397">MVC ve Razor tanılama.</span><span class="sxs-lookup"><span data-stu-id="22e38-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="22e38-398">Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="22e38-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="22e38-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="22e38-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="22e38-400">Eşleşen bilgileri yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="22e38-400">Route matching information.</span></span> |
| <span data-ttu-id="22e38-401">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="22e38-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="22e38-402">Bağlantı başlatın, durdurun ve canlı yanıtları koruyun.</span><span class="sxs-lookup"><span data-stu-id="22e38-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="22e38-403">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="22e38-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="22e38-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="22e38-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="22e38-405">Sunulan dosyalar.</span><span class="sxs-lookup"><span data-stu-id="22e38-405">Files served.</span></span> |
| <span data-ttu-id="22e38-406">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="22e38-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="22e38-407">Genel Entity Framework Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="22e38-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="22e38-408">Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="22e38-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="22e38-409">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="22e38-409">Log scopes</span></span>

 <span data-ttu-id="22e38-410">*Kapsam* bir mantıksal işlemler kümesini gruplandırabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="22e38-411">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="22e38-412">Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="22e38-413">Kapsam, <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemi tarafından `IDisposable` döndürülen ve atılana kadar sürer olan bir türdür.</span><span class="sxs-lookup"><span data-stu-id="22e38-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="22e38-414">Bir `using` blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="22e38-415">Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="22e38-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="22e38-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e38-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="22e38-417">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol günlükçü seçeneğinin yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="22e38-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="22e38-418">Yapılandırma hakkında bilgi için [yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="22e38-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="22e38-419">Her günlük iletisi kapsamlı bilgiler içerir:</span><span class="sxs-lookup"><span data-stu-id="22e38-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="22e38-420">Yerleşik günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="22e38-420">Built-in logging providers</span></span>

<span data-ttu-id="22e38-421">ASP.NET Core aşağıdaki sağlayıcıları sevk eder:</span><span class="sxs-lookup"><span data-stu-id="22e38-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="22e38-422">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="22e38-423">H</span><span class="sxs-lookup"><span data-stu-id="22e38-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="22e38-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="22e38-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="22e38-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="22e38-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="22e38-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="22e38-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="22e38-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="22e38-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="22e38-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="22e38-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="22e38-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="22e38-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="22e38-430">Stdout ve ASP.NET Core modüllü hata ayıklama günlüğü hakkında bilgi için bkz <xref:test/troubleshoot-azure-iis> . ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="22e38-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="22e38-431">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-431">Console provider</span></span>

<span data-ttu-id="22e38-432">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi, günlük çıktısını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="22e38-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="22e38-433">Konsol günlüğü çıkışını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="22e38-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="22e38-434">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-434">Debug provider</span></span>

<span data-ttu-id="22e38-435">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (`Debug.WriteLine` Yöntem çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="22e38-436">Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="22e38-437">Olay kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-437">Event Source provider</span></span>

<span data-ttu-id="22e38-438">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, adıyla `Microsoft-Extensions-Logging`bir olay kaynağı çapraz platformuna yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="22e38-439">Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="22e38-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="22e38-440">Olay kaynak sağlayıcısı, konağı oluşturmak için çağrıldığında `CreateDefaultBuilder` otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="22e38-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="22e38-441">DotNet izleme araçları</span><span class="sxs-lookup"><span data-stu-id="22e38-441">dotnet trace tooling</span></span>

<span data-ttu-id="22e38-442">[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) Aracı, çalışan bir Işlemin .NET Core izlemelerinin toplanmasını sağlayan platformlar arası CLI genel aracıdır.</span><span class="sxs-lookup"><span data-stu-id="22e38-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="22e38-443">Araç, kullanarak <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verilerini toplar <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="22e38-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="22e38-444">DotNet Trace araçları komutunu aşağıdaki komutla birlikte yüklersiniz:</span><span class="sxs-lookup"><span data-stu-id="22e38-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="22e38-445">Bir uygulamadan izleme toplamak için DotNet Trace araçları kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="22e38-446">Uygulama Konağı ile `CreateDefaultBuilder`derlenmezse, [olay kaynak sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="22e38-447">`dotnet run` Komutuyla uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="22e38-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="22e38-448">.NET Core uygulamasının işlem tanımlayıcısını (PID) belirleme:</span><span class="sxs-lookup"><span data-stu-id="22e38-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="22e38-449">Windows 'ta aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="22e38-450">Görev Yöneticisi (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="22e38-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="22e38-451">Tasklist komutu</span><span class="sxs-lookup"><span data-stu-id="22e38-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="22e38-452">Get-Process PowerShell komutu</span><span class="sxs-lookup"><span data-stu-id="22e38-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="22e38-453">Linux 'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="22e38-454">Uygulamanın derlemesi ile aynı ada sahip olan işlem için PID 'i bulun.</span><span class="sxs-lookup"><span data-stu-id="22e38-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="22e38-455">`dotnet trace` Komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="22e38-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="22e38-456">Genel komut sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="22e38-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="22e38-457">PowerShell komut kabuğu kullanırken, `--providers` değeri tek tırnak işaretleri (`'`) içine alın:</span><span class="sxs-lookup"><span data-stu-id="22e38-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="22e38-458">Windows dışı platformlarda, çıkış izleme dosyasının biçimini `-f speedscope` olarak `speedscope`değiştirme seçeneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="22e38-459">Sözcükle</span><span class="sxs-lookup"><span data-stu-id="22e38-459">Keyword</span></span> | <span data-ttu-id="22e38-460">Açıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="22e38-461">1</span><span class="sxs-lookup"><span data-stu-id="22e38-461">1</span></span>       | <span data-ttu-id="22e38-462">İçin `LoggingEventSource`meta olayları günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="22e38-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="22e38-463">Olayları günlüğe kaydetmez `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="22e38-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="22e38-464">2</span><span class="sxs-lookup"><span data-stu-id="22e38-464">2</span></span>       | <span data-ttu-id="22e38-465">Çağrıldığında `Message` olayı `ILogger.Log()` açar.</span><span class="sxs-lookup"><span data-stu-id="22e38-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="22e38-466">Programlı (biçimlendirilmedi) bir şekilde bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="22e38-467">4</span><span class="sxs-lookup"><span data-stu-id="22e38-467">4</span></span>       | <span data-ttu-id="22e38-468">Çağrıldığında `FormatMessage` olayı `ILogger.Log()` açar.</span><span class="sxs-lookup"><span data-stu-id="22e38-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="22e38-469">, Bilgilerin biçimlendirilen dize sürümünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="22e38-470">8</span><span class="sxs-lookup"><span data-stu-id="22e38-470">8</span></span>       | <span data-ttu-id="22e38-471">Çağrıldığında `MessageJson` olayı `ILogger.Log()` açar.</span><span class="sxs-lookup"><span data-stu-id="22e38-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="22e38-472">Bağımsız değişkenlerin JSON gösterimini sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="22e38-473">Olay düzeyi</span><span class="sxs-lookup"><span data-stu-id="22e38-473">Event Level</span></span> | <span data-ttu-id="22e38-474">Açıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="22e38-475">0</span><span class="sxs-lookup"><span data-stu-id="22e38-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="22e38-476">1</span><span class="sxs-lookup"><span data-stu-id="22e38-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="22e38-477">2</span><span class="sxs-lookup"><span data-stu-id="22e38-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="22e38-478">3</span><span class="sxs-lookup"><span data-stu-id="22e38-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="22e38-479">4</span><span class="sxs-lookup"><span data-stu-id="22e38-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="22e38-480">5</span><span class="sxs-lookup"><span data-stu-id="22e38-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="22e38-481">`FilterSpecs`için `{Logger Category}` girdiler ve `{Event Level}` ek günlük filtreleme koşullarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="22e38-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="22e38-482">Girişleri `FilterSpecs` noktalı virgül (`;`) ile ayırın.</span><span class="sxs-lookup"><span data-stu-id="22e38-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="22e38-483">Bir Windows komut kabuğu ( `--providers` değer etrafında tek tırnak**olmadan** ) ile örnek:</span><span class="sxs-lookup"><span data-stu-id="22e38-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="22e38-484">Yukarıdaki komut şunları etkinleştirir:</span><span class="sxs-lookup"><span data-stu-id="22e38-484">The preceding command activates:</span></span>

   * <span data-ttu-id="22e38-485">Hatalar (`4``2`) için biçimlendirilen dizeler () üretmek üzere olay kaynağı günlükçüsü.</span><span class="sxs-lookup"><span data-stu-id="22e38-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="22e38-486">`Microsoft.AspNetCore.Hosting``Informational` günlüğe kaydetme düzeyinde (`4`) günlüğe kaydetme.</span><span class="sxs-lookup"><span data-stu-id="22e38-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="22e38-487">ENTER tuşuna veya CTRL + C tuşlarına basarak DotNet izleme araçlarını durdurun.</span><span class="sxs-lookup"><span data-stu-id="22e38-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="22e38-488">İzleme, `dotnet trace` komutun yürütüldüğü klasörde *Trace. NetTrace* adıyla kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="22e38-489">Trace 'i [PerfView](#perfview)ile açın.</span><span class="sxs-lookup"><span data-stu-id="22e38-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="22e38-490">*Trace. NetTrace* dosyasını açın ve izleme olaylarını araştırın.</span><span class="sxs-lookup"><span data-stu-id="22e38-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="22e38-491">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="22e38-491">For more information, see:</span></span>

* <span data-ttu-id="22e38-492">[Performans Analizi yardımcı programı Için izleme (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core belgeleri)</span><span class="sxs-lookup"><span data-stu-id="22e38-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="22e38-493">[Performans Analizi yardımcı programı (DotNet-Trace) Için izleme](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (DotNet/Diagnostics GitHub deposu belgeleri)</span><span class="sxs-lookup"><span data-stu-id="22e38-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="22e38-494">[Loggingeventsource sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API tarayıcısı)</span><span class="sxs-lookup"><span data-stu-id="22e38-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="22e38-495">[Loggingeventsource başvuru kaynağı (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; farklı bir sürüme yönelik başvuru kaynağını almak için dalı olarak `release/{Version}`değiştirin, burada `{Version}` ASP.NET Core sürümü istenen sürümdür.</span><span class="sxs-lookup"><span data-stu-id="22e38-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="22e38-496">[PerfView](#perfview) &ndash; olay kaynağı izlemelerini görüntülemek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="22e38-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="22e38-497">PerfView</span><span class="sxs-lookup"><span data-stu-id="22e38-497">Perfview</span></span>

<span data-ttu-id="22e38-498">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="22e38-499">ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="22e38-500">Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="22e38-501">(Dizenin başlangıcında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="22e38-501">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="22e38-503">Windows olay günlüğü sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-503">Windows EventLog provider</span></span>

<span data-ttu-id="22e38-504">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.</span><span class="sxs-lookup"><span data-stu-id="22e38-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="22e38-505">[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) , geçiş yapmanızı <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="22e38-506">`null` Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="22e38-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="22e38-507">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="22e38-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="22e38-508">`SourceName`&ndash; ".NET çalışma zamanı"</span><span class="sxs-lookup"><span data-stu-id="22e38-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="22e38-509">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="22e38-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="22e38-510">Olaylar, [Uyarı düzeyi ve üzeri](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="22e38-511">Olayları daha düşük günlüğe kaydetmek `Warning`için, günlük düzeyini açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="22e38-512">Örneğin, *appSettings. JSON* dosyasına aşağıdakini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="22e38-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="22e38-513">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-513">TraceSource provider</span></span>

<span data-ttu-id="22e38-514">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="22e38-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="22e38-515">[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="22e38-516">Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine).</span><span class="sxs-lookup"><span data-stu-id="22e38-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="22e38-517">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="22e38-518">Azure App Service sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="22e38-518">Azure App Service provider</span></span>

<span data-ttu-id="22e38-519">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="22e38-520">Sağlayıcı paketi, paylaşılan çerçeveye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="22e38-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="22e38-521">Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="22e38-522">Sağlayıcı ayarlarını yapılandırmak için aşağıdaki örnekte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> gösterildiği <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>gibi ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="22e38-523">App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder.</span><span class="sxs-lookup"><span data-stu-id="22e38-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="22e38-524">Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="22e38-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="22e38-525">**Uygulama günlüğü (dosya sistemi)**</span><span class="sxs-lookup"><span data-stu-id="22e38-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="22e38-526">**Uygulama günlüğü (blob)**</span><span class="sxs-lookup"><span data-stu-id="22e38-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="22e38-527">Günlük dosyaları için varsayılan konum *D:\\Home\\LogFiles\\uygulama* klasöründedir ve varsayılan dosya adı *Diagnostics-YYYYMMDD. txt*' dir.</span><span class="sxs-lookup"><span data-stu-id="22e38-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="22e38-528">Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="22e38-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="22e38-529">Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid}-ApplicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="22e38-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="22e38-530">Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="22e38-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="22e38-531">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya blob 'lar için yerel geliştirme depolamasına yazmazsa, bu, hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="22e38-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="22e38-532">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="22e38-532">Azure log streaming</span></span>

<span data-ttu-id="22e38-533">Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:</span><span class="sxs-lookup"><span data-stu-id="22e38-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="22e38-534">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="22e38-534">The app server</span></span>
* <span data-ttu-id="22e38-535">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="22e38-535">The web server</span></span>
* <span data-ttu-id="22e38-536">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="22e38-536">Failed request tracing</span></span>

<span data-ttu-id="22e38-537">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="22e38-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="22e38-538">Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="22e38-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="22e38-539">**Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="22e38-540">Günlük **düzeyini**seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-540">Choose the log **Level**.</span></span> <span data-ttu-id="22e38-541">Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="22e38-542">Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="22e38-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="22e38-543">Uygulama tarafından `ILogger` arabirim aracılığıyla günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="22e38-544">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="22e38-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="22e38-545">[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="22e38-546">Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="22e38-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="22e38-547">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="22e38-548">Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="22e38-549">Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.</span><span class="sxs-lookup"><span data-stu-id="22e38-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="22e38-550">ASP.NET 4. x için [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="22e38-551">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="22e38-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="22e38-552">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="22e38-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="22e38-553">[ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="22e38-554">[.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="22e38-555">[Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="22e38-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="22e38-556">Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .</span><span class="sxs-lookup"><span data-stu-id="22e38-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="22e38-557">Üçüncü taraf günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="22e38-557">Third-party logging providers</span></span>

<span data-ttu-id="22e38-558">ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="22e38-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="22e38-559">[ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="22e38-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="22e38-561">[Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="22e38-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="22e38-562">[KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="22e38-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="22e38-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="22e38-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="22e38-564">[Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="22e38-565">[NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="22e38-566">[Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="22e38-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="22e38-567">[Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="22e38-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="22e38-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="22e38-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="22e38-569">Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="22e38-570">Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="22e38-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="22e38-571">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="22e38-572">Günlüğe kaydetme `ILoggerFactory` çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="22e38-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="22e38-573">Daha fazla bilgi için bkz. her sağlayıcının belgeleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="22e38-574">Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="22e38-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22e38-575">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="22e38-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="22e38-576">[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="22e38-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="22e38-577">.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="22e38-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="22e38-578">Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="22e38-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="22e38-579">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22e38-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="22e38-580">Sağlayıcı Ekle</span><span class="sxs-lookup"><span data-stu-id="22e38-580">Add providers</span></span>

<span data-ttu-id="22e38-581">Günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="22e38-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="22e38-582">Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar.</span><span class="sxs-lookup"><span data-stu-id="22e38-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="22e38-583">Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="22e38-584">Bir sağlayıcı eklemek için sağlayıcının `Add{provider name}` uzantı yöntemini *program.cs*içinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="22e38-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="22e38-585">Yukarıdaki kod, ve `Microsoft.Extensions.Logging` `Microsoft.Extensions.Configuration`için başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="22e38-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="22e38-586">Varsayılan proje şablonu, aşağıdaki <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>günlük sağlayıcılarını ekleyen çağırır:</span><span class="sxs-lookup"><span data-stu-id="22e38-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="22e38-587">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-587">Console</span></span>
* <span data-ttu-id="22e38-588">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-588">Debug</span></span>
* <span data-ttu-id="22e38-589">EventSource (ASP.NET Core 2,2 ' den başlayarak)</span><span class="sxs-lookup"><span data-stu-id="22e38-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="22e38-590">Kullanıyorsanız `CreateDefaultBuilder`, varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="22e38-591">Öğesini <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>çağırın ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="22e38-592">Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="22e38-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="22e38-593">Günlükleri oluştur</span><span class="sxs-lookup"><span data-stu-id="22e38-593">Create logs</span></span>

<span data-ttu-id="22e38-594">Günlükler oluşturmak için bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesnesi kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="22e38-595">Bir Web uygulamasında veya barındırılan hizmette, bir `ILogger` bağımlılık ekleme (dı) kaynağından yararlanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="22e38-596">Konak dışı konsol uygulamalarında, `LoggerFactory` oluşturmak için öğesini kullanın. `ILogger`</span><span class="sxs-lookup"><span data-stu-id="22e38-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="22e38-597">Aşağıdaki ASP.NET Core örnek, kategorisi olarak içeren `TodoApiSample.Pages.AboutModel` bir günlükçü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="22e38-598">Günlük *kategorisi* , her günlük ile ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="22e38-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="22e38-599">Dı `ILogger<T>` tarafından belirtilen örnek, kategori `T` olarak tam nitelikli adı olan Günlükler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="22e38-600">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi olan `Information` Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="22e38-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="22e38-601">Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="22e38-602">[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="22e38-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="22e38-603">Başlangıçta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-603">Create logs in Startup</span></span>

<span data-ttu-id="22e38-604">`Startup` Sınıf içindeki günlükleri yazmak için, Oluşturucu imzasına bir `ILogger` parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="22e38-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="22e38-605">Program sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-605">Create logs in the Program class</span></span>

<span data-ttu-id="22e38-606">`Program` Sınıf içindeki günlükleri yazmak IÇIN, dı 'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="22e38-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="22e38-607">Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="22e38-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="22e38-608">Ancak, ayrı bir günlükçü kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-608">However, a separate logger can be used.</span></span> <span data-ttu-id="22e38-609">Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="22e38-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="22e38-610">`AddSerilog`, içinde `Log.Logger`belirtilen statik yapılandırmayı kullanır:</span><span class="sxs-lookup"><span data-stu-id="22e38-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="22e38-611">Zaman uyumsuz günlükçü yöntemi yok</span><span class="sxs-lookup"><span data-stu-id="22e38-611">No asynchronous logger methods</span></span>

<span data-ttu-id="22e38-612">Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="22e38-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="22e38-613">Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="22e38-614">Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="22e38-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="22e38-615">Örneğin, SQL Server için oturum açıyorsanız `Log` `Log` Yöntemler zaman uyumlu olduğundan, bunu doğrudan bir yöntemde yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="22e38-616">Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin.</span><span class="sxs-lookup"><span data-stu-id="22e38-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="22e38-617">Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="22e38-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="22e38-618">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="22e38-618">Configuration</span></span>

<span data-ttu-id="22e38-619">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="22e38-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="22e38-620">Dosya biçimleri (ıNı, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="22e38-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="22e38-621">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-621">Command-line arguments.</span></span>
* <span data-ttu-id="22e38-622">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-622">Environment variables.</span></span>
* <span data-ttu-id="22e38-623">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="22e38-624">Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.</span><span class="sxs-lookup"><span data-stu-id="22e38-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="22e38-625">[Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="22e38-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="22e38-626">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="22e38-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="22e38-627">Örneğin, günlük yapılandırma genellikle uygulama ayarları dosyalarının `Logging` bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="22e38-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="22e38-628">Aşağıdaki örnek tipik bir appSettings 'in içeriğini gösterir *. Development. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="22e38-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="22e38-629">Özelliği `Logging` , ve günlük `LogLevel` sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="22e38-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="22e38-630">Altındaki `LogLevel` `Logging` özelliği, Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="22e38-631">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe kaydedilir ve diğer tüm diğerleri `Debug` düzeyinde günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="22e38-632">Günlük sağlayıcılarını belirt `Logging` altındaki diğer özellikler.</span><span class="sxs-lookup"><span data-stu-id="22e38-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="22e38-633">Örnek, konsol sağlayıcısına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="22e38-633">The example is for the Console provider.</span></span> <span data-ttu-id="22e38-634">Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa, `IncludeScopes` etkinleştirilip etkinleştirilmeyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="22e38-635">Bir sağlayıcı özelliği (örnekte olduğu `Console` gibi), bir `LogLevel` özellik de belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="22e38-636">`LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="22e38-637">' De `Logging.{providername}.LogLevel`düzeyler belirtilirse, içinde `Logging.LogLevel`ayarlanan her şeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="22e38-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="22e38-638">Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez.</span><span class="sxs-lookup"><span data-stu-id="22e38-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="22e38-639">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="22e38-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="22e38-640">Örneğin, ayar dosyalarını okumak için tarafından `CreateDefaultBuilder` eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), günlük yapılandırmasını varsayılan olarak yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="22e38-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="22e38-641">Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="22e38-642">Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için <xref:fundamentals/configuration/index>bkz..</span><span class="sxs-lookup"><span data-stu-id="22e38-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="22e38-643">Örnek günlüğe kaydetme çıkışı</span><span class="sxs-lookup"><span data-stu-id="22e38-643">Sample logging output</span></span>

<span data-ttu-id="22e38-644">Yukarıdaki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında Günlükler konsolunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="22e38-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="22e38-645">Konsol çıkışının bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22e38-645">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="22e38-646">Yukarıdaki Günlükler, üzerinde `http://localhost:5000/api/todo/0`örnek uygulamaya bir http get isteği yapılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="22e38-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="22e38-647">Visual Studio 'da örnek uygulamayı çalıştırdığınızda hata ayıklama penceresinde göründükleri günlüklere yönelik bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22e38-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="22e38-648">Yukarıdaki bölümde gösterilen `ILogger` çağrılar tarafından oluşturulan Günlükler "TodoApi" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="22e38-649">"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır.</span><span class="sxs-lookup"><span data-stu-id="22e38-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="22e38-650">ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="22e38-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="22e38-651">Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="22e38-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="22e38-652">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="22e38-652">NuGet packages</span></span>

<span data-ttu-id="22e38-653">`ILogger` Ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde ve bu uygulamalar için varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="22e38-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="22e38-654">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="22e38-654">Log category</span></span>

<span data-ttu-id="22e38-655">Bir `ILogger` nesne oluşturulduğunda, için bir *Kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="22e38-656">Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="22e38-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="22e38-657">Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="22e38-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="22e38-658">Kategori `ILogger<T>` `T` olarak tam nitelikli `ILogger` tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="22e38-659">Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="22e38-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="22e38-660">`ILogger<T>`, tam nitelikli tür `CreateLogger` adı ile çağırma ile eşdeğerdir `T`.</span><span class="sxs-lookup"><span data-stu-id="22e38-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="22e38-661">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="22e38-661">Log level</span></span>

<span data-ttu-id="22e38-662">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="22e38-663">Günlük düzeyi önem derecesini veya önemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="22e38-664">Örneğin, bir yöntem normal olarak sona `Information` erdiğinde ve `Warning` bir yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="22e38-665">Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:</span><span class="sxs-lookup"><span data-stu-id="22e38-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="22e38-666">Yukarıdaki kodda, ilk parametre [günlük olay kimliğidir](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="22e38-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="22e38-667">İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="22e38-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="22e38-668">Yöntem parametreleri bu makalenin ilerleyen kısımlarında bulunan [ileti şablonu bölümünde](#log-message-template) açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="22e38-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="22e38-669">Yöntem adında düzeyi (örneğin, `LogInformation` ve `LogWarning`) Içeren günlük yöntemleri, [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="22e38-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="22e38-670">Bu yöntemler bir `LogLevel` parametre `Log` alan bir yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="22e38-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="22e38-671">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak söz dizimi görece karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="22e38-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="22e38-672">Daha fazla bilgi için bkz <xref:Microsoft.Extensions.Logging.ILogger> . ve [günlükçü uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="22e38-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="22e38-673">ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="22e38-674">İzleme = 0</span><span class="sxs-lookup"><span data-stu-id="22e38-674">Trace = 0</span></span>

  <span data-ttu-id="22e38-675">Genellikle yalnızca hata ayıklama için değerli bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="22e38-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="22e38-676">Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="22e38-677">*Varsayılan olarak devre dışıdır.*</span><span class="sxs-lookup"><span data-stu-id="22e38-677">*Disabled by default.*</span></span>

* <span data-ttu-id="22e38-678">Hata Ayıkla = 1</span><span class="sxs-lookup"><span data-stu-id="22e38-678">Debug = 1</span></span>

  <span data-ttu-id="22e38-679">Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="22e38-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="22e38-680">Örnek: `Entering method Configure with flag set to true.` yüksek `Debug` günlük hacimden dolayı yalnızca sorun giderirken üretim ortamında düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="22e38-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="22e38-681">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="22e38-681">Information = 2</span></span>

  <span data-ttu-id="22e38-682">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="22e38-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="22e38-683">Bu günlüklerde genellikle uzun süreli bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="22e38-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="22e38-684">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="22e38-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="22e38-685">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="22e38-685">Warning = 3</span></span>

  <span data-ttu-id="22e38-686">Uygulama akışında anormal veya beklenmedik olaylar için.</span><span class="sxs-lookup"><span data-stu-id="22e38-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="22e38-687">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="22e38-688">İşlenmiş özel durumlar, `Warning` günlük düzeyini kullanmak için yaygın bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="22e38-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="22e38-689">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="22e38-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="22e38-690">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="22e38-690">Error = 4</span></span>

  <span data-ttu-id="22e38-691">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="22e38-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="22e38-692">Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="22e38-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="22e38-693">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="22e38-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="22e38-694">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="22e38-694">Critical = 5</span></span>

  <span data-ttu-id="22e38-695">Anında ilgilenilmesi gereken hatalarda.</span><span class="sxs-lookup"><span data-stu-id="22e38-695">For failures that require immediate attention.</span></span> <span data-ttu-id="22e38-696">Örnekler: veri kaybı senaryoları, disk alanı yetersiz.</span><span class="sxs-lookup"><span data-stu-id="22e38-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="22e38-697">Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="22e38-698">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="22e38-698">For example:</span></span>

* <span data-ttu-id="22e38-699">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="22e38-699">In production:</span></span>
  * <span data-ttu-id="22e38-700">Geçiş `Information` düzeyinde günlüğe `Trace` kaydetme, yüksek hacimli ayrıntılı günlük iletileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="22e38-701">Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, düzey `Trace` iletiler `Information` üzerinden yüksek hacimli, düşük maliyetli bir veri deposuna oturum açın.</span><span class="sxs-lookup"><span data-stu-id="22e38-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="22e38-702">`Warning` Düzeylerde oturum açma işlemi genellikle daha az, daha küçük günlük iletileri `Critical` üretir.</span><span class="sxs-lookup"><span data-stu-id="22e38-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="22e38-703">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.</span><span class="sxs-lookup"><span data-stu-id="22e38-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="22e38-704">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="22e38-704">During development:</span></span>
  * <span data-ttu-id="22e38-705">Konsola `Warning` iletiler `Critical` aracılığıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="22e38-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="22e38-706">Sorun `Trace` giderirken `Information` iletiler aracılığıyla ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="22e38-707">Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="22e38-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="22e38-708">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="22e38-709">Bu makalenin önceki kısımlarında yer alınan günlük örnekleri, günlüklerin `Information` altında tutulur, bu `Debug` nedenle `Trace` hiçbir veya düzeyi günlük oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="22e38-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="22e38-710">Günlükleri göstermek `Debug` için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22e38-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="22e38-711">Günlüğe olay KIMLIĞI</span><span class="sxs-lookup"><span data-stu-id="22e38-711">Log event ID</span></span>

<span data-ttu-id="22e38-712">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="22e38-713">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="22e38-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="22e38-714">Olay KIMLIĞI bir olay kümesini ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="22e38-714">An event ID associates a set of events.</span></span> <span data-ttu-id="22e38-715">Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="22e38-716">Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="22e38-717">Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor.</span><span class="sxs-lookup"><span data-stu-id="22e38-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="22e38-718">Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:</span><span class="sxs-lookup"><span data-stu-id="22e38-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="22e38-719">Günlük iletisi şablonu</span><span class="sxs-lookup"><span data-stu-id="22e38-719">Log message template</span></span>

<span data-ttu-id="22e38-720">Her günlük bir ileti şablonunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-720">Each log specifies a message template.</span></span> <span data-ttu-id="22e38-721">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="22e38-722">Sayılar değil, yer tutucular için adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="22e38-723">Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası.</span><span class="sxs-lookup"><span data-stu-id="22e38-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="22e38-724">Aşağıdaki kodda, parametre adlarının ileti şablonunda sıra dışı olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="22e38-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="22e38-725">Bu kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="22e38-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="22e38-726">Günlüğe kaydetme altyapısı bu şekilde çalışarak, günlük sağlayıcılarının [yapılandırılmış günlüğe yazma olarak da bilinen anlamsal günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulayabilmesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="22e38-727">Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="22e38-728">Bu bilgiler, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="22e38-729">Örneğin, günlükçü yönteminin şuna benzer şekilde göründüğünü varsayın:</span><span class="sxs-lookup"><span data-stu-id="22e38-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="22e38-730">Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı, günlük verilerinde sorguları basitleştiren `ID` ve `RequestTime` özelliklere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="22e38-731">Bir sorgu, belirli `RequestTime` bir aralıktaki tüm günlükleri, kısa mesajdan zaman aşımını ayrıştırmadan bulabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="22e38-732">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="22e38-732">Logging exceptions</span></span>

<span data-ttu-id="22e38-733">Günlükçü yöntemlerinin, aşağıdaki örnekte olduğu gibi bir özel durum iletmenizi sağlayan aşırı yüklemeleri vardır:</span><span class="sxs-lookup"><span data-stu-id="22e38-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="22e38-734">Özel durum bilgileri farklı yollarla farklı sağlayıcılarda işler.</span><span class="sxs-lookup"><span data-stu-id="22e38-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="22e38-735">Yukarıda gösterilen koddan hata ayıklama sağlayıcısı çıktısına bir örnek aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="22e38-736">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="22e38-736">Log filtering</span></span>

<span data-ttu-id="22e38-737">Belirli bir sağlayıcı ve kategori için en az bir günlük düzeyi veya tüm sağlayıcılar ya da tüm kategoriler için belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="22e38-738">Minimum düzeyin altındaki tüm Günlükler bu sağlayıcıya aktarılmaz, bu nedenle görüntülenmez veya depolanmaz.</span><span class="sxs-lookup"><span data-stu-id="22e38-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="22e38-739">Tüm günlükleri gizlemek için en düşük `LogLevel.None` günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="22e38-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="22e38-740">Öğesinin `LogLevel.None` tamsayı değeri 6 ' dır `LogLevel.Critical` (5) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="22e38-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="22e38-741">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="22e38-741">Create filter rules in configuration</span></span>

<span data-ttu-id="22e38-742">Proje şablonu kodu, konsol `CreateDefaultBuilder` , hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="22e38-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="22e38-743">`CreateDefaultBuilder` Yöntemi, [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, bir `Logging` bölümde yapılandırmayı aramak için günlük kaydı yapar.</span><span class="sxs-lookup"><span data-stu-id="22e38-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="22e38-744">Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="22e38-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="22e38-745">Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="22e38-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="22e38-746">Bir `ILogger` nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="22e38-747">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="22e38-747">Filter rules in code</span></span>

<span data-ttu-id="22e38-748">Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="22e38-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="22e38-749">İkincisi `AddFilter` , hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="22e38-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="22e38-750">Birincisi `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="22e38-751">Filtreleme kuralları nasıl uygulanır</span><span class="sxs-lookup"><span data-stu-id="22e38-751">How filtering rules are applied</span></span>

<span data-ttu-id="22e38-752">Yapılandırma verileri ve önceki örneklerde `AddFilter` gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22e38-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="22e38-753">İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.</span><span class="sxs-lookup"><span data-stu-id="22e38-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="22e38-754">Sayı</span><span class="sxs-lookup"><span data-stu-id="22e38-754">Number</span></span> | <span data-ttu-id="22e38-755">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="22e38-755">Provider</span></span>      | <span data-ttu-id="22e38-756">Şununla başlayan Kategoriler...</span><span class="sxs-lookup"><span data-stu-id="22e38-756">Categories that begin with ...</span></span>          | <span data-ttu-id="22e38-757">En düşük günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="22e38-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="22e38-758">1</span><span class="sxs-lookup"><span data-stu-id="22e38-758">1</span></span>      | <span data-ttu-id="22e38-759">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-759">Debug</span></span>         | <span data-ttu-id="22e38-760">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="22e38-760">All categories</span></span>                          | <span data-ttu-id="22e38-761">Bilgi</span><span class="sxs-lookup"><span data-stu-id="22e38-761">Information</span></span>       |
| <span data-ttu-id="22e38-762">2</span><span class="sxs-lookup"><span data-stu-id="22e38-762">2</span></span>      | <span data-ttu-id="22e38-763">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-763">Console</span></span>       | <span data-ttu-id="22e38-764">Microsoft. AspNetCore. Mvc. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="22e38-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="22e38-765">Uyarı</span><span class="sxs-lookup"><span data-stu-id="22e38-765">Warning</span></span>           |
| <span data-ttu-id="22e38-766">3</span><span class="sxs-lookup"><span data-stu-id="22e38-766">3</span></span>      | <span data-ttu-id="22e38-767">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-767">Console</span></span>       | <span data-ttu-id="22e38-768">Microsoft. AspNetCore. Mvc. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="22e38-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="22e38-769">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-769">Debug</span></span>             |
| <span data-ttu-id="22e38-770">4</span><span class="sxs-lookup"><span data-stu-id="22e38-770">4</span></span>      | <span data-ttu-id="22e38-771">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-771">Console</span></span>       | <span data-ttu-id="22e38-772">Microsoft. AspNetCore. Mvc. Razor</span><span class="sxs-lookup"><span data-stu-id="22e38-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="22e38-773">Hata</span><span class="sxs-lookup"><span data-stu-id="22e38-773">Error</span></span>             |
| <span data-ttu-id="22e38-774">5</span><span class="sxs-lookup"><span data-stu-id="22e38-774">5</span></span>      | <span data-ttu-id="22e38-775">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-775">Console</span></span>       | <span data-ttu-id="22e38-776">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="22e38-776">All categories</span></span>                          | <span data-ttu-id="22e38-777">Bilgi</span><span class="sxs-lookup"><span data-stu-id="22e38-777">Information</span></span>       |
| <span data-ttu-id="22e38-778">6</span><span class="sxs-lookup"><span data-stu-id="22e38-778">6</span></span>      | <span data-ttu-id="22e38-779">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="22e38-779">All providers</span></span> | <span data-ttu-id="22e38-780">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="22e38-780">All categories</span></span>                          | <span data-ttu-id="22e38-781">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-781">Debug</span></span>             |
| <span data-ttu-id="22e38-782">7</span><span class="sxs-lookup"><span data-stu-id="22e38-782">7</span></span>      | <span data-ttu-id="22e38-783">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="22e38-783">All providers</span></span> | <span data-ttu-id="22e38-784">Sistem</span><span class="sxs-lookup"><span data-stu-id="22e38-784">System</span></span>                                  | <span data-ttu-id="22e38-785">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-785">Debug</span></span>             |
| <span data-ttu-id="22e38-786">8</span><span class="sxs-lookup"><span data-stu-id="22e38-786">8</span></span>      | <span data-ttu-id="22e38-787">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-787">Debug</span></span>         | <span data-ttu-id="22e38-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="22e38-788">Microsoft</span></span>                               | <span data-ttu-id="22e38-789">İzleme</span><span class="sxs-lookup"><span data-stu-id="22e38-789">Trace</span></span>             |

<span data-ttu-id="22e38-790">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="22e38-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="22e38-791">Bir `ILogger` örnek tarafından yazılan tüm iletiler, seçilen kurallara göre filtrelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="22e38-792">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="22e38-793">Belirli bir kategori için oluşturulan her sağlayıcı `ILogger` için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="22e38-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="22e38-794">Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="22e38-795">Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="22e38-796">Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="22e38-797">Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="22e38-798">Birden çok kural seçilirse, **son** olanı götürün.</span><span class="sxs-lookup"><span data-stu-id="22e38-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="22e38-799">Hiçbir kural seçilmezse, kullanın `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="22e38-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="22e38-800">Yukarıdaki kurallar listesinde, "Microsoft. AspNetCore. Mvc `ILogger` . Razor. RazorViewEngine" kategorisi için bir nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="22e38-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="22e38-801">Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="22e38-802">Kural 8 ' i en özeldir, yani seçili olanı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="22e38-803">Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="22e38-804">Kural 3 en özeldir.</span><span class="sxs-lookup"><span data-stu-id="22e38-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="22e38-805">Elde edilen `ILogger` örnek, hata ayıklama `Trace` sağlayıcısına düzeyi ve üzeri Günlükler gönderir.</span><span class="sxs-lookup"><span data-stu-id="22e38-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="22e38-806">`Debug` Düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="22e38-807">Sağlayıcı diğer adları</span><span class="sxs-lookup"><span data-stu-id="22e38-807">Provider aliases</span></span>

<span data-ttu-id="22e38-808">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="22e38-809">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="22e38-810">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-810">Console</span></span>
* <span data-ttu-id="22e38-811">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="22e38-811">Debug</span></span>
* <span data-ttu-id="22e38-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="22e38-812">EventSource</span></span>
* <span data-ttu-id="22e38-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="22e38-813">EventLog</span></span>
* <span data-ttu-id="22e38-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="22e38-814">TraceSource</span></span>
* <span data-ttu-id="22e38-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="22e38-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="22e38-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="22e38-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="22e38-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="22e38-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="22e38-818">Varsayılan en düşük düzey</span><span class="sxs-lookup"><span data-stu-id="22e38-818">Default minimum level</span></span>

<span data-ttu-id="22e38-819">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kural uygulanmaz geçerli olan en düşük düzey ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="22e38-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="22e38-820">Aşağıdaki örnekte, en düşük düzeyin nasıl ayarlanacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="22e38-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="22e38-821">En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer olur `Information`ve `Trace` `Debug` bu, günlüklerin yok sayılacağı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="22e38-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="22e38-822">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="22e38-822">Filter functions</span></span>

<span data-ttu-id="22e38-823">Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="22e38-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="22e38-824">İşlevindeki kodun sağlayıcı türü, kategorisi ve günlük düzeyine erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="22e38-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="22e38-825">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="22e38-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="22e38-826">Sistem kategorileri ve Düzeyler</span><span class="sxs-lookup"><span data-stu-id="22e38-826">System categories and levels</span></span>

<span data-ttu-id="22e38-827">ASP.NET Core ve Entity Framework Core tarafından kullanılan bazı kategoriler şunlardır ve bunlardan beklenen Günlükler hakkında notlar bulunur:</span><span class="sxs-lookup"><span data-stu-id="22e38-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="22e38-828">Kategori</span><span class="sxs-lookup"><span data-stu-id="22e38-828">Category</span></span>                            | <span data-ttu-id="22e38-829">Notlar</span><span class="sxs-lookup"><span data-stu-id="22e38-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="22e38-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="22e38-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="22e38-831">Genel ASP.NET Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="22e38-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="22e38-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="22e38-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="22e38-833">Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı.</span><span class="sxs-lookup"><span data-stu-id="22e38-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="22e38-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="22e38-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="22e38-835">İzin verilen konaklar.</span><span class="sxs-lookup"><span data-stu-id="22e38-835">Hosts allowed.</span></span> |
| <span data-ttu-id="22e38-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="22e38-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="22e38-837">HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları.</span><span class="sxs-lookup"><span data-stu-id="22e38-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="22e38-838">Hangi barındırma başlangıç derlemeleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="22e38-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="22e38-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="22e38-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="22e38-840">MVC ve Razor tanılama.</span><span class="sxs-lookup"><span data-stu-id="22e38-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="22e38-841">Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="22e38-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="22e38-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="22e38-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="22e38-843">Eşleşen bilgileri yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="22e38-843">Route matching information.</span></span> |
| <span data-ttu-id="22e38-844">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="22e38-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="22e38-845">Bağlantı başlatın, durdurun ve canlı yanıtları koruyun.</span><span class="sxs-lookup"><span data-stu-id="22e38-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="22e38-846">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="22e38-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="22e38-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="22e38-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="22e38-848">Sunulan dosyalar.</span><span class="sxs-lookup"><span data-stu-id="22e38-848">Files served.</span></span> |
| <span data-ttu-id="22e38-849">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="22e38-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="22e38-850">Genel Entity Framework Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="22e38-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="22e38-851">Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="22e38-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="22e38-852">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="22e38-852">Log scopes</span></span>

 <span data-ttu-id="22e38-853">*Kapsam* bir mantıksal işlemler kümesini gruplandırabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="22e38-854">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="22e38-855">Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="22e38-856">Kapsam, <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemi tarafından `IDisposable` döndürülen ve atılana kadar sürer olan bir türdür.</span><span class="sxs-lookup"><span data-stu-id="22e38-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="22e38-857">Bir `using` blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="22e38-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="22e38-858">Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="22e38-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="22e38-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e38-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="22e38-860">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol günlükçü seçeneğinin yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="22e38-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="22e38-861">Yapılandırma hakkında bilgi için [yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="22e38-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="22e38-862">Her günlük iletisi kapsamlı bilgiler içerir:</span><span class="sxs-lookup"><span data-stu-id="22e38-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="22e38-863">Yerleşik günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="22e38-863">Built-in logging providers</span></span>

<span data-ttu-id="22e38-864">ASP.NET Core aşağıdaki sağlayıcıları sevk eder:</span><span class="sxs-lookup"><span data-stu-id="22e38-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="22e38-865">Konsol</span><span class="sxs-lookup"><span data-stu-id="22e38-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="22e38-866">H</span><span class="sxs-lookup"><span data-stu-id="22e38-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="22e38-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="22e38-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="22e38-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="22e38-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="22e38-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="22e38-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="22e38-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="22e38-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="22e38-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="22e38-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="22e38-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="22e38-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="22e38-873">Stdout ve ASP.NET Core modüllü hata ayıklama günlüğü hakkında bilgi için bkz <xref:test/troubleshoot-azure-iis> . ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="22e38-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="22e38-874">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-874">Console provider</span></span>

<span data-ttu-id="22e38-875">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi, günlük çıktısını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="22e38-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="22e38-876">Konsol günlüğü çıkışını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="22e38-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="22e38-877">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-877">Debug provider</span></span>

<span data-ttu-id="22e38-878">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (`Debug.WriteLine` Yöntem çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="22e38-879">Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="22e38-880">Olay kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-880">Event Source provider</span></span>

<span data-ttu-id="22e38-881">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, adıyla `Microsoft-Extensions-Logging`bir olay kaynağı çapraz platformuna yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="22e38-882">Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="22e38-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="22e38-883">Olay kaynak sağlayıcısı, konağı oluşturmak için çağrıldığında `CreateDefaultBuilder` otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="22e38-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="22e38-884">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="22e38-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="22e38-885">ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="22e38-886">Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="22e38-887">(Dizenin başlangıcında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="22e38-887">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="22e38-889">Windows olay günlüğü sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-889">Windows EventLog provider</span></span>

<span data-ttu-id="22e38-890">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.</span><span class="sxs-lookup"><span data-stu-id="22e38-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="22e38-891">[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) , geçiş yapmanızı <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="22e38-892">`null` Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="22e38-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="22e38-893">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="22e38-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="22e38-894">`SourceName`&ndash; ".NET çalışma zamanı"</span><span class="sxs-lookup"><span data-stu-id="22e38-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="22e38-895">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="22e38-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="22e38-896">Olaylar, [Uyarı düzeyi ve üzeri](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="22e38-897">Olayları daha düşük günlüğe kaydetmek `Warning`için, günlük düzeyini açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="22e38-898">Örneğin, *appSettings. JSON* dosyasına aşağıdakini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="22e38-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="22e38-899">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="22e38-899">TraceSource provider</span></span>

<span data-ttu-id="22e38-900">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="22e38-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="22e38-901">[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="22e38-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="22e38-902">Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine).</span><span class="sxs-lookup"><span data-stu-id="22e38-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="22e38-903">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="22e38-904">Azure App Service sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="22e38-904">Azure App Service provider</span></span>

<span data-ttu-id="22e38-905">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="22e38-906">Sağlayıcı paketi [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="22e38-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="22e38-907">.NET Framework veya `Microsoft.AspNetCore.App` metapackage 'e başvuru yaparken, sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="22e38-908"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Aşırı yükleme, geçiş yapmanızı sağlar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="22e38-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="22e38-909">Ayarlar nesnesi, günlük çıkış şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="22e38-910">(*Çıktı şablonu* , bir `ILogger` yöntem çağrısıyla birlikte sağlandığının yanı sıra tüm günlüklere uygulanan bir ileti şablonudur.)</span><span class="sxs-lookup"><span data-stu-id="22e38-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="22e38-911">App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder.</span><span class="sxs-lookup"><span data-stu-id="22e38-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="22e38-912">Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="22e38-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="22e38-913">**Uygulama günlüğü (dosya sistemi)**</span><span class="sxs-lookup"><span data-stu-id="22e38-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="22e38-914">**Uygulama günlüğü (blob)**</span><span class="sxs-lookup"><span data-stu-id="22e38-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="22e38-915">Günlük dosyaları için varsayılan konum *D:\\Home\\LogFiles\\uygulama* klasöründedir ve varsayılan dosya adı *Diagnostics-YYYYMMDD. txt*' dir.</span><span class="sxs-lookup"><span data-stu-id="22e38-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="22e38-916">Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="22e38-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="22e38-917">Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid}-ApplicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="22e38-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="22e38-918">Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="22e38-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="22e38-919">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya blob 'lar için yerel geliştirme depolamasına yazmazsa, bu, hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="22e38-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="22e38-920">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="22e38-920">Azure log streaming</span></span>

<span data-ttu-id="22e38-921">Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:</span><span class="sxs-lookup"><span data-stu-id="22e38-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="22e38-922">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="22e38-922">The app server</span></span>
* <span data-ttu-id="22e38-923">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="22e38-923">The web server</span></span>
* <span data-ttu-id="22e38-924">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="22e38-924">Failed request tracing</span></span>

<span data-ttu-id="22e38-925">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="22e38-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="22e38-926">Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="22e38-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="22e38-927">**Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="22e38-928">Günlük **düzeyini**seçin.</span><span class="sxs-lookup"><span data-stu-id="22e38-928">Choose the log **Level**.</span></span> <span data-ttu-id="22e38-929">Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="22e38-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="22e38-930">Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="22e38-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="22e38-931">Uygulama tarafından `ILogger` arabirim aracılığıyla günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="22e38-932">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="22e38-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="22e38-933">[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar.</span><span class="sxs-lookup"><span data-stu-id="22e38-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="22e38-934">Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="22e38-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="22e38-935">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22e38-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="22e38-936">Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="22e38-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="22e38-937">Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.</span><span class="sxs-lookup"><span data-stu-id="22e38-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="22e38-938">ASP.NET 4. x için [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="22e38-939">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="22e38-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="22e38-940">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="22e38-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="22e38-941">[ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="22e38-942">[.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="22e38-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="22e38-943">[Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="22e38-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="22e38-944">Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .</span><span class="sxs-lookup"><span data-stu-id="22e38-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="22e38-945">Üçüncü taraf günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="22e38-945">Third-party logging providers</span></span>

<span data-ttu-id="22e38-946">ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="22e38-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="22e38-947">[ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="22e38-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="22e38-949">[Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="22e38-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="22e38-950">[KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="22e38-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="22e38-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="22e38-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="22e38-952">[Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="22e38-953">[NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="22e38-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="22e38-954">[Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="22e38-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="22e38-955">[Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="22e38-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="22e38-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="22e38-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="22e38-957">Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="22e38-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="22e38-958">Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="22e38-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="22e38-959">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="22e38-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="22e38-960">Günlüğe kaydetme `ILoggerFactory` çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="22e38-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="22e38-961">Daha fazla bilgi için bkz. her sağlayıcının belgeleri.</span><span class="sxs-lookup"><span data-stu-id="22e38-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="22e38-962">Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="22e38-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22e38-963">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="22e38-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
