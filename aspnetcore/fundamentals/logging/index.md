---
title: .NET Core ve ASP.NET Core'da Oturum Açma
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet paketi tarafından sağlanan günlük çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791569"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="9bfe4-103">.NET Core ve ASP.NET Core'da Oturum Açma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bfe4-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9bfe4-105">.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="9bfe4-106">Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="9bfe4-107">Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalarındandır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="9bfe4-108">Bu kod parçacıklarının günlüğe özgü [bölümleri, Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanan herhangi bir .NET Core uygulaması için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="9bfe4-109">Web konsolu olmayan bir uygulamada Genel Ana Bilgisayar'ın nasıl kullanılacağına bir örnek olarak, [Arka Plan Görevleri örnek uygulamasının](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) <xref:fundamentals/host/hosted-services> *Program.cs* dosyasına bakın .</span><span class="sxs-lookup"><span data-stu-id="9bfe4-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="9bfe4-110">Genel Ana Bilgisayar olmayan uygulamaların günlük [kodu, sağlayıcıların eklenme](#add-providers) ve [kaydedicilerin oluşturulma](#create-logs)şeklinde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="9bfe4-111">Ana bilgisayar kodu örnekleri makalenin bu bölümlerinde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="9bfe4-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="9bfe4-113">Sağlayıcılar ekleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-113">Add providers</span></span>

<span data-ttu-id="9bfe4-114">Bir günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="9bfe4-115">Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="9bfe4-116">Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="9bfe4-117">Genel Ana Bilgisayar kullanan bir uygulamaya sağlayıcı eklemek `Add{provider name}` *için, sağlayıcının*uzantı yöntemini Program.cs:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="9bfe4-118">Barındırılamayan bir konsol uygulamasında, `Add{provider name}` sağlayıcının uzatma `LoggerFactory`yöntemini arayarak aşağıdakileri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="9bfe4-119">`LoggerFactory`ve `AddConsole` için `using` `Microsoft.Extensions.Logging`bir ifade gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="9bfe4-120">Varsayılan ASP.NET Core proje <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>şablonları çağrı , aşağıdaki günlük sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="9bfe4-121">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="9bfe4-122">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="9bfe4-123">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="9bfe4-124">[EventLog](#windows-eventlog-provider) (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="9bfe4-125">Varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="9bfe4-126">Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="9bfe4-127">Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="9bfe4-128">Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-128">Create logs</span></span>

<span data-ttu-id="9bfe4-129">Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="9bfe4-130">Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="9bfe4-131">Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .</span><span class="sxs-lookup"><span data-stu-id="9bfe4-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="9bfe4-132">Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="9bfe4-133">Günlük *kategorisi,* her günlükle ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="9bfe4-134">DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="9bfe4-135">Aşağıdaki ana bilgisayar olmayan konsol uygulaması örneği, `LoggingConsoleApp.Program` kategori olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="9bfe4-136">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="9bfe4-137">Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="9bfe4-138">[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="9bfe4-139">Program sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-139">Create logs in the Program class</span></span>

<span data-ttu-id="9bfe4-140">ASP.NET Core `Program` uygulamasının sınıfına günlük yazmak için, ana bilgisayarı yaptıktan sonra DI'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="9bfe4-141">Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="9bfe4-142">Ancak, ayrı bir logger kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-142">However, a separate logger can be used.</span></span> <span data-ttu-id="9bfe4-143">Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateHostBuilder`açmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="9bfe4-144">`AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="9bfe4-145">Başlangıç sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-145">Create logs in the Startup class</span></span>

<span data-ttu-id="9bfe4-146">ASP.NET Core uygulaması `Startup.Configure` nın yönteminde günlük yazmak `ILogger` için yöntem imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="9bfe4-147">`Startup.ConfigureServices` Yöntemdeki DI kapsayıcı kurulumu tamamlanmadan önce günlükleri yazma desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="9bfe4-148">`Startup` Logger enjeksiyon yapıcı içine desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="9bfe4-149">Yöntem imzasına `Startup.ConfigureServices` logger enjeksiyonu desteklenmez</span><span class="sxs-lookup"><span data-stu-id="9bfe4-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="9bfe4-150">Bu kısıtlamanın nedeni, günlüğe kaydetmenin DI'ye ve yapılandırmaya bağlı olmasıdır, bu da sırayla DI'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="9bfe4-151">DI kapsayıcısı bitene kadar `ConfigureServices` kurulmadı.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="9bfe4-152">Web Barındırıcısı için `Startup` ayrı bir DI kapsayıcı oluşturulduğundan, ASP.NET Core'un önceki sürümlerinde bir logger'ın oluşturucu enjeksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="9bfe4-153">Genel Ana Bilgisayar için neden yalnızca bir kapsayıcı oluşturulduğu hakkında bilgi [için, kesme değişikliği duyurusuna](https://github.com/aspnet/Announcements/issues/353)bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="9bfe4-154">Bağlı bir hizmeti yapılandırmanız `ILogger<T>`gerekiyorsa, bunu yine de yapıcı enjeksiyon kullanarak veya bir fabrika yöntemi sağlayarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="9bfe4-155">Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="9bfe4-156">Örneğin, bir mülkü DI'den bir hizmetle doldurmanız gerektiğini varsayalım:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="9bfe4-157">Önceki vurgulanan kod, `Func` DI kapsayıcısının bir örneğini oluşturmak için `MyService`ilk kez çalışan bir koddur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="9bfe4-158">Kayıtlı hizmetlerden herhangi biri bu şekilde erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor-webassembly"></a><span data-ttu-id="9bfe4-159">Blazor WebAssembly'de günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-159">Create logs in Blazor WebAssembly</span></span>

<span data-ttu-id="9bfe4-160">Blazor WebAssembly uygulamalarında oturum `WebAssemblyHostBuilder.Logging` açma `Program.Main`özelliği şu şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-160">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="9bfe4-161">Özellik `Logging` türündedir, <xref:Microsoft.Extensions.Logging.ILoggingBuilder>bu nedenle kullanılabilir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> tüm uzantı `Logging`yöntemleri de mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-161">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="9bfe4-162">Asynchronous logger yöntemleri yok</span><span class="sxs-lookup"><span data-stu-id="9bfe4-162">No asynchronous logger methods</span></span>

<span data-ttu-id="9bfe4-163">Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-163">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="9bfe4-164">Günlük veri deponuz yavaşsa, doğrudan yazmayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-164">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="9bfe4-165">Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-165">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="9bfe4-166">Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-166">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="9bfe4-167">Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-167">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="9bfe4-168">Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-168">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="9bfe4-169">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-169">Configuration</span></span>

<span data-ttu-id="9bfe4-170">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-170">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="9bfe4-171">Dosya biçimleri (INI, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-171">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="9bfe4-172">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-172">Command-line arguments.</span></span>
* <span data-ttu-id="9bfe4-173">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-173">Environment variables.</span></span>
* <span data-ttu-id="9bfe4-174">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-174">In-memory .NET objects.</span></span>
* <span data-ttu-id="9bfe4-175">Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-175">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="9bfe4-176">[Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-176">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="9bfe4-177">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-177">Custom providers (installed or created).</span></span>

<span data-ttu-id="9bfe4-178">Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-178">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="9bfe4-179">Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-179">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="9bfe4-180">Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-180">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="9bfe4-181">Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-181">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="9bfe4-182">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-182">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="9bfe4-183">Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-183">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="9bfe4-184">Örnek Konsol sağlayıcısı içindir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-184">The example is for the Console provider.</span></span> <span data-ttu-id="9bfe4-185">Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-185">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="9bfe4-186">Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-186">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="9bfe4-187">`LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-187">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="9bfe4-188">Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-188">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="9bfe4-189">Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-189">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="9bfe4-190">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-190">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="9bfe4-191">Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-191">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="9bfe4-192">Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-192">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="9bfe4-193">Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="9bfe4-194">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-194">Sample logging output</span></span>

<span data-ttu-id="9bfe4-195">Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="9bfe4-196">Konsol çıkışına bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-196">Here's an example of console output:</span></span>

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

<span data-ttu-id="9bfe4-197">Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="9bfe4-198">Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="9bfe4-199">Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApiSample" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="9bfe4-200">"Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="9bfe4-201">ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="9bfe4-202">Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-202">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="9bfe4-203">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-203">NuGet packages</span></span>

<span data-ttu-id="9bfe4-204">Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-204">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="9bfe4-205">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-205">Log category</span></span>

<span data-ttu-id="9bfe4-206">Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-206">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="9bfe4-207">Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-207">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="9bfe4-208">Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-208">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="9bfe4-209">Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-209">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="9bfe4-210">Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-210">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="9bfe4-211">`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-211">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="9bfe4-212">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-212">Log level</span></span>

<span data-ttu-id="9bfe4-213">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-213">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="9bfe4-214">Günlük düzeyi önem veya önemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-214">The log level indicates the severity or importance.</span></span> <span data-ttu-id="9bfe4-215">Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-215">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="9bfe4-216">Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-216">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9bfe4-217">Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-217">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="9bfe4-218">İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-218">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="9bfe4-219">Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-219">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="9bfe4-220">Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-220">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="9bfe4-221">Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-221">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="9bfe4-222">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-222">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="9bfe4-223">Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-223">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="9bfe4-224">ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-224">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="9bfe4-225">İz = 0</span><span class="sxs-lookup"><span data-stu-id="9bfe4-225">Trace = 0</span></span>

  <span data-ttu-id="9bfe4-226">Genellikle yalnızca hata ayıklama için değerli olan bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-226">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="9bfe4-227">Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-227">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="9bfe4-228">*Varsayılan olarak devre dışı bırakılır.*</span><span class="sxs-lookup"><span data-stu-id="9bfe4-228">*Disabled by default.*</span></span>

* <span data-ttu-id="9bfe4-229">Hata Ayıklama = 1</span><span class="sxs-lookup"><span data-stu-id="9bfe4-229">Debug = 1</span></span>

  <span data-ttu-id="9bfe4-230">Geliştirme ve hata ayıklama yararlı olabilir bilgi için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-230">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="9bfe4-231">Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-231">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="9bfe4-232">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="9bfe4-232">Information = 2</span></span>

  <span data-ttu-id="9bfe4-233">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-233">For tracking the general flow of the app.</span></span> <span data-ttu-id="9bfe4-234">Bu günlüklerin genellikle bazı uzun vadeli değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-234">These logs typically have some long-term value.</span></span> <span data-ttu-id="9bfe4-235">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-235">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="9bfe4-236">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="9bfe4-236">Warning = 3</span></span>

  <span data-ttu-id="9bfe4-237">Uygulama akışındaki anormal veya beklenmeyen olaylar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-237">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="9bfe4-238">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-238">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="9bfe4-239">İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-239">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="9bfe4-240">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-240">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="9bfe4-241">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="9bfe4-241">Error = 4</span></span>

  <span data-ttu-id="9bfe4-242">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-242">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="9bfe4-243">Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-243">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="9bfe4-244">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-244">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="9bfe4-245">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="9bfe4-245">Critical = 5</span></span>

  <span data-ttu-id="9bfe4-246">Acil müdahale gerektiren arızalar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-246">For failures that require immediate attention.</span></span> <span data-ttu-id="9bfe4-247">Örnekler: disk alanı dışında veri kaybı senaryoları.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-247">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="9bfe4-248">Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-248">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="9bfe4-249">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-249">For example:</span></span>

* <span data-ttu-id="9bfe4-250">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-250">In production:</span></span>
  * <span data-ttu-id="9bfe4-251">`Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-251">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="9bfe4-252">Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-252">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="9bfe4-253">Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-253">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="9bfe4-254">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-254">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="9bfe4-255">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-255">During development:</span></span>
  * <span data-ttu-id="9bfe4-256">İletileri konsola kaydedin. `Warning` `Critical`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-256">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="9bfe4-257">Sorun `Trace` `Information` giderme iletileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-257">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="9bfe4-258">Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-258">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="9bfe4-259">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-259">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="9bfe4-260">Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-260">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="9bfe4-261">Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-261">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="9bfe4-262">Günlük olay kimliği</span><span class="sxs-lookup"><span data-stu-id="9bfe4-262">Log event ID</span></span>

<span data-ttu-id="9bfe4-263">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-263">Each log can specify an *event ID*.</span></span> <span data-ttu-id="9bfe4-264">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-264">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="9bfe4-265">Olay kimliği, bir dizi olayı ilişkilendirer.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-265">An event ID associates a set of events.</span></span> <span data-ttu-id="9bfe4-266">Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-266">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="9bfe4-267">Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-267">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="9bfe4-268">Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-268">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="9bfe4-269">Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-269">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="9bfe4-270">Günlük ileti şablonu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-270">Log message template</span></span>

<span data-ttu-id="9bfe4-271">Her günlük bir ileti şablonu belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-271">Each log specifies a message template.</span></span> <span data-ttu-id="9bfe4-272">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-272">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="9bfe4-273">Yer tutucular için adları kullanın, sayılar için değil.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-273">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9bfe4-274">Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-274">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="9bfe4-275">Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-275">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="9bfe4-276">Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-276">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="9bfe4-277">Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-277">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="9bfe4-278">Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-278">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="9bfe4-279">Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-279">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="9bfe4-280">Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-280">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="9bfe4-281">Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-281">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="9bfe4-282">Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-282">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="9bfe4-283">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-283">Logging exceptions</span></span>

<span data-ttu-id="9bfe4-284">Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-284">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="9bfe4-285">Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-285">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="9bfe4-286">Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-286">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="9bfe4-287">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-287">Log filtering</span></span>

<span data-ttu-id="9bfe4-288">Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-288">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="9bfe4-289">Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-289">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="9bfe4-290">Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-290">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="9bfe4-291">İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-291">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="9bfe4-292">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-292">Create filter rules in configuration</span></span>

<span data-ttu-id="9bfe4-293">Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-293">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="9bfe4-294">Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-294">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="9bfe4-295">Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-295">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="9bfe4-296">Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-296">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="9bfe4-297">Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-297">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="9bfe4-298">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-298">Filter rules in code</span></span>

<span data-ttu-id="9bfe4-299">Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-299">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="9bfe4-300">İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-300">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="9bfe4-301">İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-301">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="9bfe4-302">Filtreleme kuralları nasıl uygulanır?</span><span class="sxs-lookup"><span data-stu-id="9bfe4-302">How filtering rules are applied</span></span>

<span data-ttu-id="9bfe4-303">Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-303">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="9bfe4-304">İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-304">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="9bfe4-305">Sayı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-305">Number</span></span> | <span data-ttu-id="9bfe4-306">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-306">Provider</span></span>      | <span data-ttu-id="9bfe4-307">Ile başlayan kategoriler ...</span><span class="sxs-lookup"><span data-stu-id="9bfe4-307">Categories that begin with ...</span></span>          | <span data-ttu-id="9bfe4-308">Minimum günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-308">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="9bfe4-309">1</span><span class="sxs-lookup"><span data-stu-id="9bfe4-309">1</span></span>      | <span data-ttu-id="9bfe4-310">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-310">Debug</span></span>         | <span data-ttu-id="9bfe4-311">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="9bfe4-311">All categories</span></span>                          | <span data-ttu-id="9bfe4-312">Bilgi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-312">Information</span></span>       |
| <span data-ttu-id="9bfe4-313">2</span><span class="sxs-lookup"><span data-stu-id="9bfe4-313">2</span></span>      | <span data-ttu-id="9bfe4-314">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-314">Console</span></span>       | <span data-ttu-id="9bfe4-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="9bfe4-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="9bfe4-316">Uyarı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-316">Warning</span></span>           |
| <span data-ttu-id="9bfe4-317">3</span><span class="sxs-lookup"><span data-stu-id="9bfe4-317">3</span></span>      | <span data-ttu-id="9bfe4-318">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-318">Console</span></span>       | <span data-ttu-id="9bfe4-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="9bfe4-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="9bfe4-320">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-320">Debug</span></span>             |
| <span data-ttu-id="9bfe4-321">4</span><span class="sxs-lookup"><span data-stu-id="9bfe4-321">4</span></span>      | <span data-ttu-id="9bfe4-322">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-322">Console</span></span>       | <span data-ttu-id="9bfe4-323">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="9bfe4-323">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="9bfe4-324">Hata</span><span class="sxs-lookup"><span data-stu-id="9bfe4-324">Error</span></span>             |
| <span data-ttu-id="9bfe4-325">5</span><span class="sxs-lookup"><span data-stu-id="9bfe4-325">5</span></span>      | <span data-ttu-id="9bfe4-326">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-326">Console</span></span>       | <span data-ttu-id="9bfe4-327">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="9bfe4-327">All categories</span></span>                          | <span data-ttu-id="9bfe4-328">Bilgi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-328">Information</span></span>       |
| <span data-ttu-id="9bfe4-329">6</span><span class="sxs-lookup"><span data-stu-id="9bfe4-329">6</span></span>      | <span data-ttu-id="9bfe4-330">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-330">All providers</span></span> | <span data-ttu-id="9bfe4-331">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="9bfe4-331">All categories</span></span>                          | <span data-ttu-id="9bfe4-332">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-332">Debug</span></span>             |
| <span data-ttu-id="9bfe4-333">7</span><span class="sxs-lookup"><span data-stu-id="9bfe4-333">7</span></span>      | <span data-ttu-id="9bfe4-334">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-334">All providers</span></span> | <span data-ttu-id="9bfe4-335">Sistem</span><span class="sxs-lookup"><span data-stu-id="9bfe4-335">System</span></span>                                  | <span data-ttu-id="9bfe4-336">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-336">Debug</span></span>             |
| <span data-ttu-id="9bfe4-337">8</span><span class="sxs-lookup"><span data-stu-id="9bfe4-337">8</span></span>      | <span data-ttu-id="9bfe4-338">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-338">Debug</span></span>         | <span data-ttu-id="9bfe4-339">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9bfe4-339">Microsoft</span></span>                               | <span data-ttu-id="9bfe4-340">İzleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-340">Trace</span></span>             |

<span data-ttu-id="9bfe4-341">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-341">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="9bfe4-342">Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-342">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="9bfe4-343">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-343">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="9bfe4-344">Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-344">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="9bfe4-345">Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-345">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="9bfe4-346">Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-346">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="9bfe4-347">Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-347">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="9bfe4-348">Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-348">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="9bfe4-349">Birden çok kural seçilirse, **sonuncusunu** alın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-349">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="9bfe4-350">Kural seçili değilse, `MinimumLevel`kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-350">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="9bfe4-351">Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-351">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="9bfe4-352">Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-352">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="9bfe4-353">Kural 8 çok özeldir, bu yüzden seçilen kuraldır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-353">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="9bfe4-354">Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-354">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="9bfe4-355">Kural 3 çok özeldir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-355">Rule 3 is most specific.</span></span>

<span data-ttu-id="9bfe4-356">Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-356">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="9bfe4-357">`Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-357">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="9bfe4-358">Sağlayıcı diğer adlar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-358">Provider aliases</span></span>

<span data-ttu-id="9bfe4-359">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-359">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="9bfe4-360">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-360">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="9bfe4-361">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-361">Console</span></span>
* <span data-ttu-id="9bfe4-362">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-362">Debug</span></span>
* <span data-ttu-id="9bfe4-363">EventSource</span><span class="sxs-lookup"><span data-stu-id="9bfe4-363">EventSource</span></span>
* <span data-ttu-id="9bfe4-364">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9bfe4-364">EventLog</span></span>
* <span data-ttu-id="9bfe4-365">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9bfe4-365">TraceSource</span></span>
* <span data-ttu-id="9bfe4-366">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9bfe4-366">AzureAppServicesFile</span></span>
* <span data-ttu-id="9bfe4-367">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9bfe4-367">AzureAppServicesBlob</span></span>
* <span data-ttu-id="9bfe4-368">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9bfe4-368">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="9bfe4-369">Varsayılan minimum düzey</span><span class="sxs-lookup"><span data-stu-id="9bfe4-369">Default minimum level</span></span>

<span data-ttu-id="9bfe4-370">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-370">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="9bfe4-371">Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-371">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="9bfe4-372">Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-372">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="9bfe4-373">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-373">Filter functions</span></span>

<span data-ttu-id="9bfe4-374">Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-374">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="9bfe4-375">İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-375">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="9bfe4-376">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-376">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="9bfe4-377">Sistem kategorileri ve düzeyleri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-377">System categories and levels</span></span>

<span data-ttu-id="9bfe4-378">ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-378">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="9bfe4-379">Kategori</span><span class="sxs-lookup"><span data-stu-id="9bfe4-379">Category</span></span>                            | <span data-ttu-id="9bfe4-380">Notlar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-380">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="9bfe4-381">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="9bfe4-381">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="9bfe4-382">General ASP.NET Core teşhisi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-382">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="9bfe4-383">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="9bfe4-383">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="9bfe4-384">Hangi anahtarlar düşünüldü, bulundu ve kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-384">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="9bfe4-385">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="9bfe4-385">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="9bfe4-386">Ev sahiplerine izin verildi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-386">Hosts allowed.</span></span> |
| <span data-ttu-id="9bfe4-387">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="9bfe4-387">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="9bfe4-388">HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-388">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="9bfe4-389">Hangi barındırma başlangıç meclisleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-389">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="9bfe4-390">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="9bfe4-390">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="9bfe4-391">MVC ve Razor teşhis.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-391">MVC and Razor diagnostics.</span></span> <span data-ttu-id="9bfe4-392">Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-392">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="9bfe4-393">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="9bfe4-393">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="9bfe4-394">Rota eşleştirme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-394">Route matching information.</span></span> |
| <span data-ttu-id="9bfe4-395">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="9bfe4-395">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="9bfe4-396">Bağlantı başlatın, durdurun ve yanıtları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-396">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="9bfe4-397">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-397">HTTPS certificate information.</span></span> |
| <span data-ttu-id="9bfe4-398">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="9bfe4-398">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="9bfe4-399">Dosyalar servis edilebis.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-399">Files served.</span></span> |
| <span data-ttu-id="9bfe4-400">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9bfe4-400">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="9bfe4-401">Genel Varlık Çerçeve Çekirdek tanılama.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-401">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="9bfe4-402">Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-402">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="9bfe4-403">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-403">Log scopes</span></span>

 <span data-ttu-id="9bfe4-404">*Kapsam,* bir dizi mantıksal işlemi gruplayabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-404">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="9bfe4-405">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-405">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="9bfe4-406">Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-406">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="9bfe4-407">Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-407">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="9bfe4-408">Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-408">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="9bfe4-409">Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-409">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="9bfe4-410">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-410">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="9bfe4-411">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-411">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="9bfe4-412">Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-412">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="9bfe4-413">Her günlük iletisi kapsamlı bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-413">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="9bfe4-414">Yerleşik günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-414">Built-in logging providers</span></span>

<span data-ttu-id="9bfe4-415">ASP.NET Core aşağıdaki sağlayıcıları gemiler:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-415">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="9bfe4-416">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-416">Console</span></span>](#console-provider)
* [<span data-ttu-id="9bfe4-417">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-417">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="9bfe4-418">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-418">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="9bfe4-419">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9bfe4-419">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="9bfe4-420">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9bfe4-420">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="9bfe4-421">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9bfe4-421">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9bfe4-422">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9bfe4-422">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9bfe4-423">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9bfe4-423">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="9bfe4-424">ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis></span><span class="sxs-lookup"><span data-stu-id="9bfe4-424">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="9bfe4-425">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-425">Console provider</span></span>

<span data-ttu-id="9bfe4-426">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-426">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="9bfe4-427">Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-427">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="9bfe4-428">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-428">Debug provider</span></span>

<span data-ttu-id="9bfe4-429">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-429">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="9bfe4-430">Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-430">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="9bfe4-431">Olay Kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-431">Event Source provider</span></span>

<span data-ttu-id="9bfe4-432">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-432">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="9bfe4-433">Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-433">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="9bfe4-434">Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-434">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="9bfe4-435">dotnet izleme takım</span><span class="sxs-lookup"><span data-stu-id="9bfe4-435">dotnet trace tooling</span></span>

<span data-ttu-id="9bfe4-436">[Dotnet izleme](/dotnet/core/diagnostics/dotnet-trace) aracı, çalışan bir işlemin .NET Core izlerinin toplanmasını sağlayan bir çapraz platform CLI global aracıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-436">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="9bfe4-437">Araç <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verilerini bir <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-437">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="9bfe4-438">Aşağıdaki komutla dotnet izleme aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-438">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="9bfe4-439">Bir uygulamadan iz toplamak için dotnet izleme aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-439">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="9bfe4-440">Uygulama ana bilgisayarı `CreateDefaultBuilder`oluşturmuyorsa, Olay Kaynağı [sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-440">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="9bfe4-441">Uygulamayı komutla `dotnet run` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-441">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="9bfe4-442">.NET Core uygulamasının işlem tanımlayıcısını (PID) belirleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-442">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="9bfe4-443">Windows'da aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-443">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="9bfe4-444">Görev Yöneticisi (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-444">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="9bfe4-445">görev listesi komutu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-445">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="9bfe4-446">Get-Process Powershell komutu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-446">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="9bfe4-447">Linux'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-447">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="9bfe4-448">Uygulamanın derlemesi ile aynı ada sahip işlem için PID'yi bulun.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-448">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="9bfe4-449">Komutu `dotnet trace` uygulayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-449">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="9bfe4-450">Genel komut sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-450">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="9bfe4-451">PowerShell komut uyruğunu kullanırken, değeri tek tırnak içine alabilen `--providers` (`'`):</span><span class="sxs-lookup"><span data-stu-id="9bfe4-451">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="9bfe4-452">Windows olmayan platformlarda, `-f speedscope` çıktı izleme dosyasının biçimini `speedscope`''ye değiştirme seçeneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-452">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="9bfe4-453">Anahtar kelime</span><span class="sxs-lookup"><span data-stu-id="9bfe4-453">Keyword</span></span> | <span data-ttu-id="9bfe4-454">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-454">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="9bfe4-455">1</span><span class="sxs-lookup"><span data-stu-id="9bfe4-455">1</span></span>       | <span data-ttu-id="9bfe4-456">Hakkında meta olayları `LoggingEventSource`günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-456">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="9bfe4-457">Olayları kaydetmez). `ILogger`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-457">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="9bfe4-458">2</span><span class="sxs-lookup"><span data-stu-id="9bfe4-458">2</span></span>       | <span data-ttu-id="9bfe4-459">Çağrıldığında `Message` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-459">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="9bfe4-460">Bilgileri programatik (biçimlendirilmemiş) bir şekilde sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-460">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="9bfe4-461">4</span><span class="sxs-lookup"><span data-stu-id="9bfe4-461">4</span></span>       | <span data-ttu-id="9bfe4-462">Çağrıldığında `FormatMessage` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-462">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="9bfe4-463">Bilgilerin biçimlendirilmiş dize sürümünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-463">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="9bfe4-464">8</span><span class="sxs-lookup"><span data-stu-id="9bfe4-464">8</span></span>       | <span data-ttu-id="9bfe4-465">Çağrıldığında `MessageJson` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-465">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="9bfe4-466">Bağımsız değişkenlerin JSON temsilini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-466">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="9bfe4-467">Etkinlik Düzeyi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-467">Event Level</span></span> | <span data-ttu-id="9bfe4-468">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-468">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="9bfe4-469">0</span><span class="sxs-lookup"><span data-stu-id="9bfe4-469">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="9bfe4-470">1</span><span class="sxs-lookup"><span data-stu-id="9bfe4-470">1</span></span>           | `Critical`      |
   | <span data-ttu-id="9bfe4-471">2</span><span class="sxs-lookup"><span data-stu-id="9bfe4-471">2</span></span>           | `Error`         |
   | <span data-ttu-id="9bfe4-472">3</span><span class="sxs-lookup"><span data-stu-id="9bfe4-472">3</span></span>           | `Warning`       |
   | <span data-ttu-id="9bfe4-473">4</span><span class="sxs-lookup"><span data-stu-id="9bfe4-473">4</span></span>           | `Informational` |
   | <span data-ttu-id="9bfe4-474">5</span><span class="sxs-lookup"><span data-stu-id="9bfe4-474">5</span></span>           | `Verbose`       |

   <span data-ttu-id="9bfe4-475">`FilterSpecs`için `{Logger Category}` girişler ve `{Event Level}` ek günlük filtreleme koşulları temsil.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-475">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="9bfe4-476">Bir `FilterSpecs` semicolon ile`;`ayrı girişleri ( ).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-476">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="9bfe4-477">Windows komut uyruğunu kullanarak örnek `--providers` (değer etrafında tek bir tırnak işareti**yok):**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-477">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="9bfe4-478">Önceki komut etkinleştirir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-478">The preceding command activates:</span></span>

   * <span data-ttu-id="9bfe4-479">Hatalar için biçimlendirilmiş dizeleri (`4`) üretmek`2`için Olay Kaynağı kaydedici ( ).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-479">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="9bfe4-480">`Microsoft.AspNetCore.Hosting``Informational` günlük düzeyinde günlüğe`4`kaydetme ( ).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-480">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="9bfe4-481">Enter tuşuna veya Ctrl+C tuşuna basarak dotnet izleme aletini durdurun.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-481">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="9bfe4-482">İzleme, `dotnet trace` komutun yürütüldüğü klasöre *trace.nettrace* adı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-482">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="9bfe4-483">[Perfview](#perfview)ile iz açın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-483">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="9bfe4-484">*trace.nettrace* dosyasını açın ve izleme olaylarını keşfedin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-484">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="9bfe4-485">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-485">For more information, see:</span></span>

* <span data-ttu-id="9bfe4-486">[Performans analizi yardımcı programı (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core dokümantasyon) için izleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="9bfe4-487">[Performans analizi yardımcı programı (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub depo belgeleri) için izleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="9bfe4-488">[LoggingEventSource Sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Tarayıcısı)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-488">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="9bfe4-489">[LoggingEventSource referans kaynağı (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Farklı bir sürüm için referans `release/{Version}`kaynağı `{Version}` elde etmek için, şubeyi , ASP.NET Core sürümü istenilen nerede değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-489">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="9bfe4-490">[Perfview](#perfview) &ndash; Olay Kaynağı izlerini görüntülemek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-490">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="9bfe4-491">Perfview</span><span class="sxs-lookup"><span data-stu-id="9bfe4-491">Perfview</span></span>

<span data-ttu-id="9bfe4-492">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-492">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="9bfe4-493">ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-493">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="9bfe4-494">PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-494">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="9bfe4-495">(Dize başında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-495">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="9bfe4-497">Windows EventLog sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-497">Windows EventLog provider</span></span>

<span data-ttu-id="9bfe4-498">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-498">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="9bfe4-499">[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-499">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="9bfe4-500">`null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-500">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="9bfe4-501">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="9bfe4-501">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="9bfe4-502">`SourceName`&ndash; ".NET Çalışma Zamanı"</span><span class="sxs-lookup"><span data-stu-id="9bfe4-502">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="9bfe4-503">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="9bfe4-503">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="9bfe4-504">Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-504">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="9bfe4-505">Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-505">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="9bfe4-506">Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-506">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="9bfe4-507">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-507">TraceSource provider</span></span>

<span data-ttu-id="9bfe4-508">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-508">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="9bfe4-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="9bfe4-510">Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-510">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="9bfe4-511">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-511">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="9bfe4-512">Azure Uygulama Hizmeti sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-512">Azure App Service provider</span></span>

<span data-ttu-id="9bfe4-513">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-513">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="9bfe4-514">Sağlayıcı paketi paylaşılan çerçeveye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-514">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="9bfe4-515">Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-515">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="9bfe4-516">Sağlayıcı ayarlarını yapılandırmak <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>için aşağıdaki örnekte gösterildiği gibi kullanın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-516">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="9bfe4-517">Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-517">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="9bfe4-518">Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-518">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="9bfe4-519">**Uygulama Günlüğü (Filesystem)**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-519">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="9bfe4-520">**Uygulama Günlüğü (Blob)**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-520">**Application Logging (Blob)**</span></span>

<span data-ttu-id="9bfe4-521">Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-521">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="9bfe4-522">Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-522">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="9bfe4-523">Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-523">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="9bfe4-524">Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-524">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="9bfe4-525">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-525">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="9bfe4-526">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-526">Azure log streaming</span></span>

<span data-ttu-id="9bfe4-527">Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-527">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="9bfe4-528">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-528">The app server</span></span>
* <span data-ttu-id="9bfe4-529">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-529">The web server</span></span>
* <span data-ttu-id="9bfe4-530">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-530">Failed request tracing</span></span>

<span data-ttu-id="9bfe4-531">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-531">To configure Azure log streaming:</span></span>

* <span data-ttu-id="9bfe4-532">Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-532">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="9bfe4-533">**Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-533">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="9bfe4-534">Günlük **Düzeyi'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-534">Choose the log **Level**.</span></span> <span data-ttu-id="9bfe4-535">Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-535">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="9bfe4-536">Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-536">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="9bfe4-537">Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-537">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="9bfe4-538">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="9bfe4-538">Azure Application Insights trace logging</span></span>

<span data-ttu-id="9bfe4-539">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-539">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="9bfe4-540">Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-540">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="9bfe4-541">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-541">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="9bfe4-542">Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-542">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="9bfe4-543">Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-543">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="9bfe4-544">ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-544">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="9bfe4-545">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-545">For more information, see the following resources:</span></span>

* [<span data-ttu-id="9bfe4-546">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="9bfe4-546">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="9bfe4-547">[ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-547">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="9bfe4-548">[.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="9bfe4-549">[Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-549">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="9bfe4-550">Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-550">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="9bfe4-551">Üçüncü taraf günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-551">Third-party logging providers</span></span>

<span data-ttu-id="9bfe4-552">ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-552">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="9bfe4-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="9bfe4-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="9bfe4-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="9bfe4-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="9bfe4-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="9bfe4-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="9bfe4-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="9bfe4-560">[Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-560">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="9bfe4-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="9bfe4-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="9bfe4-563">Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-563">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9bfe4-564">Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-564">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="9bfe4-565">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-565">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="9bfe4-566">Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-566">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="9bfe4-567">Daha fazla bilgi için her sağlayıcının belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-567">For more information, see each provider's documentation.</span></span> <span data-ttu-id="9bfe4-568">Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-568">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bfe4-569">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-569">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bfe4-570">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-570">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9bfe4-571">.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-571">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="9bfe4-572">Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-572">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="9bfe4-573">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-573">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="9bfe4-574">Sağlayıcılar ekleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-574">Add providers</span></span>

<span data-ttu-id="9bfe4-575">Bir günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-575">A logging provider displays or stores logs.</span></span> <span data-ttu-id="9bfe4-576">Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-576">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="9bfe4-577">Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-577">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="9bfe4-578">Sağlayıcı eklemek için `Add{provider name}` *sağlayıcının*uzatma yöntemini Program.cs olarak arayın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-578">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="9bfe4-579">Önceki kod için başvurular `Microsoft.Extensions.Logging` `Microsoft.Extensions.Configuration`ve .</span><span class="sxs-lookup"><span data-stu-id="9bfe4-579">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="9bfe4-580">Varsayılan proje şablonu çağrıları <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, aşağıdaki günlük sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-580">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="9bfe4-581">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-581">Console</span></span>
* <span data-ttu-id="9bfe4-582">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-582">Debug</span></span>
* <span data-ttu-id="9bfe4-583">EventSource (Core 2.2ASP.NETden itibaren)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-583">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="9bfe4-584">Kullanırsanız, `CreateDefaultBuilder`varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-584">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="9bfe4-585">Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-585">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="9bfe4-586">Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-586">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="9bfe4-587">Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-587">Create logs</span></span>

<span data-ttu-id="9bfe4-588">Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-588">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="9bfe4-589">Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-589">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="9bfe4-590">Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .</span><span class="sxs-lookup"><span data-stu-id="9bfe4-590">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="9bfe4-591">Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-591">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="9bfe4-592">Günlük *kategorisi,* her günlükle ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-592">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="9bfe4-593">DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-593">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="9bfe4-594">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-594">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="9bfe4-595">Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-595">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="9bfe4-596">[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-596">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="9bfe4-597">Başlangıç'ta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-597">Create logs in Startup</span></span>

<span data-ttu-id="9bfe4-598">`Startup` Sınıfa günlük yazmak için, `ILogger` oluşturucu imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-598">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="9bfe4-599">Program sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-599">Create logs in the Program class</span></span>

<span data-ttu-id="9bfe4-600">`Program` Sınıfa günlük yazmak için DI'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-600">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="9bfe4-601">Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-601">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="9bfe4-602">Ancak, ayrı bir logger kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-602">However, a separate logger can be used.</span></span> <span data-ttu-id="9bfe4-603">Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateWebHostBuilder`açmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-603">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="9bfe4-604">`AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-604">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="9bfe4-605">Asynchronous logger yöntemleri yok</span><span class="sxs-lookup"><span data-stu-id="9bfe4-605">No asynchronous logger methods</span></span>

<span data-ttu-id="9bfe4-606">Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-606">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="9bfe4-607">Günlük veri deponuz yavaşsa, doğrudan yazmayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-607">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="9bfe4-608">Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-608">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="9bfe4-609">Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-609">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="9bfe4-610">Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-610">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="9bfe4-611">Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-611">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="9bfe4-612">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-612">Configuration</span></span>

<span data-ttu-id="9bfe4-613">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-613">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="9bfe4-614">Dosya biçimleri (INI, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-614">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="9bfe4-615">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-615">Command-line arguments.</span></span>
* <span data-ttu-id="9bfe4-616">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-616">Environment variables.</span></span>
* <span data-ttu-id="9bfe4-617">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-617">In-memory .NET objects.</span></span>
* <span data-ttu-id="9bfe4-618">Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-618">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="9bfe4-619">[Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-619">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="9bfe4-620">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-620">Custom providers (installed or created).</span></span>

<span data-ttu-id="9bfe4-621">Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-621">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="9bfe4-622">Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-622">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="9bfe4-623">Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-623">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="9bfe4-624">Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-624">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="9bfe4-625">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-625">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="9bfe4-626">Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-626">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="9bfe4-627">Örnek Konsol sağlayıcısı içindir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-627">The example is for the Console provider.</span></span> <span data-ttu-id="9bfe4-628">Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-628">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="9bfe4-629">Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-629">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="9bfe4-630">`LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-630">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="9bfe4-631">Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-631">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="9bfe4-632">Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-632">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="9bfe4-633">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-633">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="9bfe4-634">Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-634">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="9bfe4-635">Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-635">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="9bfe4-636">Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-636">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="9bfe4-637">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-637">Sample logging output</span></span>

<span data-ttu-id="9bfe4-638">Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-638">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="9bfe4-639">Konsol çıkışına bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-639">Here's an example of console output:</span></span>

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

<span data-ttu-id="9bfe4-640">Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-640">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="9bfe4-641">Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-641">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="9bfe4-642">Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApi" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-642">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="9bfe4-643">"Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-643">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="9bfe4-644">ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-644">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="9bfe4-645">Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-645">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="9bfe4-646">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-646">NuGet packages</span></span>

<span data-ttu-id="9bfe4-647">Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-647">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="9bfe4-648">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-648">Log category</span></span>

<span data-ttu-id="9bfe4-649">Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-649">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="9bfe4-650">Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-650">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="9bfe4-651">Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-651">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="9bfe4-652">Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-652">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="9bfe4-653">Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-653">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="9bfe4-654">`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-654">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="9bfe4-655">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-655">Log level</span></span>

<span data-ttu-id="9bfe4-656">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-656">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="9bfe4-657">Günlük düzeyi önem veya önemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-657">The log level indicates the severity or importance.</span></span> <span data-ttu-id="9bfe4-658">Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-658">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="9bfe4-659">Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-659">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9bfe4-660">Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-660">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="9bfe4-661">İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-661">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="9bfe4-662">Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-662">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="9bfe4-663">Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-663">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="9bfe4-664">Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-664">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="9bfe4-665">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-665">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="9bfe4-666">Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-666">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="9bfe4-667">ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-667">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="9bfe4-668">İz = 0</span><span class="sxs-lookup"><span data-stu-id="9bfe4-668">Trace = 0</span></span>

  <span data-ttu-id="9bfe4-669">Genellikle yalnızca hata ayıklama için değerli olan bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-669">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="9bfe4-670">Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-670">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="9bfe4-671">*Varsayılan olarak devre dışı bırakılır.*</span><span class="sxs-lookup"><span data-stu-id="9bfe4-671">*Disabled by default.*</span></span>

* <span data-ttu-id="9bfe4-672">Hata Ayıklama = 1</span><span class="sxs-lookup"><span data-stu-id="9bfe4-672">Debug = 1</span></span>

  <span data-ttu-id="9bfe4-673">Geliştirme ve hata ayıklama yararlı olabilir bilgi için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-673">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="9bfe4-674">Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-674">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="9bfe4-675">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="9bfe4-675">Information = 2</span></span>

  <span data-ttu-id="9bfe4-676">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-676">For tracking the general flow of the app.</span></span> <span data-ttu-id="9bfe4-677">Bu günlüklerin genellikle bazı uzun vadeli değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-677">These logs typically have some long-term value.</span></span> <span data-ttu-id="9bfe4-678">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-678">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="9bfe4-679">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="9bfe4-679">Warning = 3</span></span>

  <span data-ttu-id="9bfe4-680">Uygulama akışındaki anormal veya beklenmeyen olaylar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-680">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="9bfe4-681">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-681">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="9bfe4-682">İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-682">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="9bfe4-683">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-683">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="9bfe4-684">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="9bfe4-684">Error = 4</span></span>

  <span data-ttu-id="9bfe4-685">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-685">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="9bfe4-686">Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-686">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="9bfe4-687">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-687">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="9bfe4-688">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="9bfe4-688">Critical = 5</span></span>

  <span data-ttu-id="9bfe4-689">Acil müdahale gerektiren arızalar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-689">For failures that require immediate attention.</span></span> <span data-ttu-id="9bfe4-690">Örnekler: disk alanı dışında veri kaybı senaryoları.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-690">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="9bfe4-691">Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-691">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="9bfe4-692">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-692">For example:</span></span>

* <span data-ttu-id="9bfe4-693">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-693">In production:</span></span>
  * <span data-ttu-id="9bfe4-694">`Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-694">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="9bfe4-695">Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-695">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="9bfe4-696">Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-696">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="9bfe4-697">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-697">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="9bfe4-698">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-698">During development:</span></span>
  * <span data-ttu-id="9bfe4-699">İletileri konsola kaydedin. `Warning` `Critical`</span><span class="sxs-lookup"><span data-stu-id="9bfe4-699">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="9bfe4-700">Sorun `Trace` `Information` giderme iletileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-700">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="9bfe4-701">Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-701">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="9bfe4-702">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-702">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="9bfe4-703">Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-703">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="9bfe4-704">Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-704">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="9bfe4-705">Günlük olay kimliği</span><span class="sxs-lookup"><span data-stu-id="9bfe4-705">Log event ID</span></span>

<span data-ttu-id="9bfe4-706">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-706">Each log can specify an *event ID*.</span></span> <span data-ttu-id="9bfe4-707">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-707">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="9bfe4-708">Olay kimliği, bir dizi olayı ilişkilendirer.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-708">An event ID associates a set of events.</span></span> <span data-ttu-id="9bfe4-709">Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-709">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="9bfe4-710">Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-710">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="9bfe4-711">Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-711">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="9bfe4-712">Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-712">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="9bfe4-713">Günlük ileti şablonu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-713">Log message template</span></span>

<span data-ttu-id="9bfe4-714">Her günlük bir ileti şablonu belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-714">Each log specifies a message template.</span></span> <span data-ttu-id="9bfe4-715">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-715">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="9bfe4-716">Yer tutucular için adları kullanın, sayılar için değil.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-716">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9bfe4-717">Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-717">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="9bfe4-718">Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-718">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="9bfe4-719">Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-719">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="9bfe4-720">Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-720">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="9bfe4-721">Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-721">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="9bfe4-722">Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-722">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="9bfe4-723">Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-723">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="9bfe4-724">Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-724">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="9bfe4-725">Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-725">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="9bfe4-726">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-726">Logging exceptions</span></span>

<span data-ttu-id="9bfe4-727">Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-727">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="9bfe4-728">Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-728">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="9bfe4-729">Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-729">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="9bfe4-730">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-730">Log filtering</span></span>

<span data-ttu-id="9bfe4-731">Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-731">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="9bfe4-732">Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-732">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="9bfe4-733">Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-733">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="9bfe4-734">İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-734">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="9bfe4-735">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="9bfe4-735">Create filter rules in configuration</span></span>

<span data-ttu-id="9bfe4-736">Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-736">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="9bfe4-737">Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-737">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="9bfe4-738">Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-738">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="9bfe4-739">Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-739">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="9bfe4-740">Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-740">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="9bfe4-741">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-741">Filter rules in code</span></span>

<span data-ttu-id="9bfe4-742">Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-742">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="9bfe4-743">İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-743">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="9bfe4-744">İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-744">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="9bfe4-745">Filtreleme kuralları nasıl uygulanır?</span><span class="sxs-lookup"><span data-stu-id="9bfe4-745">How filtering rules are applied</span></span>

<span data-ttu-id="9bfe4-746">Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-746">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="9bfe4-747">İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-747">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="9bfe4-748">Sayı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-748">Number</span></span> | <span data-ttu-id="9bfe4-749">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-749">Provider</span></span>      | <span data-ttu-id="9bfe4-750">Ile başlayan kategoriler ...</span><span class="sxs-lookup"><span data-stu-id="9bfe4-750">Categories that begin with ...</span></span>          | <span data-ttu-id="9bfe4-751">Minimum günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-751">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="9bfe4-752">1</span><span class="sxs-lookup"><span data-stu-id="9bfe4-752">1</span></span>      | <span data-ttu-id="9bfe4-753">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-753">Debug</span></span>         | <span data-ttu-id="9bfe4-754">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="9bfe4-754">All categories</span></span>                          | <span data-ttu-id="9bfe4-755">Bilgi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-755">Information</span></span>       |
| <span data-ttu-id="9bfe4-756">2</span><span class="sxs-lookup"><span data-stu-id="9bfe4-756">2</span></span>      | <span data-ttu-id="9bfe4-757">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-757">Console</span></span>       | <span data-ttu-id="9bfe4-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="9bfe4-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="9bfe4-759">Uyarı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-759">Warning</span></span>           |
| <span data-ttu-id="9bfe4-760">3</span><span class="sxs-lookup"><span data-stu-id="9bfe4-760">3</span></span>      | <span data-ttu-id="9bfe4-761">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-761">Console</span></span>       | <span data-ttu-id="9bfe4-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="9bfe4-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="9bfe4-763">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-763">Debug</span></span>             |
| <span data-ttu-id="9bfe4-764">4</span><span class="sxs-lookup"><span data-stu-id="9bfe4-764">4</span></span>      | <span data-ttu-id="9bfe4-765">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-765">Console</span></span>       | <span data-ttu-id="9bfe4-766">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="9bfe4-766">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="9bfe4-767">Hata</span><span class="sxs-lookup"><span data-stu-id="9bfe4-767">Error</span></span>             |
| <span data-ttu-id="9bfe4-768">5</span><span class="sxs-lookup"><span data-stu-id="9bfe4-768">5</span></span>      | <span data-ttu-id="9bfe4-769">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-769">Console</span></span>       | <span data-ttu-id="9bfe4-770">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="9bfe4-770">All categories</span></span>                          | <span data-ttu-id="9bfe4-771">Bilgi</span><span class="sxs-lookup"><span data-stu-id="9bfe4-771">Information</span></span>       |
| <span data-ttu-id="9bfe4-772">6</span><span class="sxs-lookup"><span data-stu-id="9bfe4-772">6</span></span>      | <span data-ttu-id="9bfe4-773">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-773">All providers</span></span> | <span data-ttu-id="9bfe4-774">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="9bfe4-774">All categories</span></span>                          | <span data-ttu-id="9bfe4-775">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-775">Debug</span></span>             |
| <span data-ttu-id="9bfe4-776">7</span><span class="sxs-lookup"><span data-stu-id="9bfe4-776">7</span></span>      | <span data-ttu-id="9bfe4-777">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-777">All providers</span></span> | <span data-ttu-id="9bfe4-778">Sistem</span><span class="sxs-lookup"><span data-stu-id="9bfe4-778">System</span></span>                                  | <span data-ttu-id="9bfe4-779">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-779">Debug</span></span>             |
| <span data-ttu-id="9bfe4-780">8</span><span class="sxs-lookup"><span data-stu-id="9bfe4-780">8</span></span>      | <span data-ttu-id="9bfe4-781">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-781">Debug</span></span>         | <span data-ttu-id="9bfe4-782">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9bfe4-782">Microsoft</span></span>                               | <span data-ttu-id="9bfe4-783">İzleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-783">Trace</span></span>             |

<span data-ttu-id="9bfe4-784">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-784">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="9bfe4-785">Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-785">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="9bfe4-786">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-786">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="9bfe4-787">Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-787">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="9bfe4-788">Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-788">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="9bfe4-789">Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-789">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="9bfe4-790">Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-790">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="9bfe4-791">Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-791">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="9bfe4-792">Birden çok kural seçilirse, **sonuncusunu** alın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-792">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="9bfe4-793">Kural seçili değilse, `MinimumLevel`kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-793">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="9bfe4-794">Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-794">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="9bfe4-795">Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-795">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="9bfe4-796">Kural 8 çok özeldir, bu yüzden seçilen kuraldır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-796">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="9bfe4-797">Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-797">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="9bfe4-798">Kural 3 çok özeldir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-798">Rule 3 is most specific.</span></span>

<span data-ttu-id="9bfe4-799">Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-799">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="9bfe4-800">`Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-800">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="9bfe4-801">Sağlayıcı diğer adlar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-801">Provider aliases</span></span>

<span data-ttu-id="9bfe4-802">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-802">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="9bfe4-803">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-803">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="9bfe4-804">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-804">Console</span></span>
* <span data-ttu-id="9bfe4-805">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-805">Debug</span></span>
* <span data-ttu-id="9bfe4-806">EventSource</span><span class="sxs-lookup"><span data-stu-id="9bfe4-806">EventSource</span></span>
* <span data-ttu-id="9bfe4-807">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9bfe4-807">EventLog</span></span>
* <span data-ttu-id="9bfe4-808">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9bfe4-808">TraceSource</span></span>
* <span data-ttu-id="9bfe4-809">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9bfe4-809">AzureAppServicesFile</span></span>
* <span data-ttu-id="9bfe4-810">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9bfe4-810">AzureAppServicesBlob</span></span>
* <span data-ttu-id="9bfe4-811">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9bfe4-811">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="9bfe4-812">Varsayılan minimum düzey</span><span class="sxs-lookup"><span data-stu-id="9bfe4-812">Default minimum level</span></span>

<span data-ttu-id="9bfe4-813">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-813">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="9bfe4-814">Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-814">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="9bfe4-815">Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-815">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="9bfe4-816">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-816">Filter functions</span></span>

<span data-ttu-id="9bfe4-817">Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-817">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="9bfe4-818">İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-818">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="9bfe4-819">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-819">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="9bfe4-820">Sistem kategorileri ve düzeyleri</span><span class="sxs-lookup"><span data-stu-id="9bfe4-820">System categories and levels</span></span>

<span data-ttu-id="9bfe4-821">ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-821">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="9bfe4-822">Kategori</span><span class="sxs-lookup"><span data-stu-id="9bfe4-822">Category</span></span>                            | <span data-ttu-id="9bfe4-823">Notlar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-823">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="9bfe4-824">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="9bfe4-824">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="9bfe4-825">General ASP.NET Core teşhisi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-825">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="9bfe4-826">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="9bfe4-826">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="9bfe4-827">Hangi anahtarlar düşünüldü, bulundu ve kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-827">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="9bfe4-828">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="9bfe4-828">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="9bfe4-829">Ev sahiplerine izin verildi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-829">Hosts allowed.</span></span> |
| <span data-ttu-id="9bfe4-830">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="9bfe4-830">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="9bfe4-831">HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-831">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="9bfe4-832">Hangi barındırma başlangıç meclisleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-832">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="9bfe4-833">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="9bfe4-833">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="9bfe4-834">MVC ve Razor teşhis.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-834">MVC and Razor diagnostics.</span></span> <span data-ttu-id="9bfe4-835">Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-835">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="9bfe4-836">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="9bfe4-836">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="9bfe4-837">Rota eşleştirme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-837">Route matching information.</span></span> |
| <span data-ttu-id="9bfe4-838">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="9bfe4-838">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="9bfe4-839">Bağlantı başlatın, durdurun ve yanıtları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-839">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="9bfe4-840">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-840">HTTPS certificate information.</span></span> |
| <span data-ttu-id="9bfe4-841">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="9bfe4-841">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="9bfe4-842">Dosyalar servis edilebis.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-842">Files served.</span></span> |
| <span data-ttu-id="9bfe4-843">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9bfe4-843">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="9bfe4-844">Genel Varlık Çerçeve Çekirdek tanılama.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-844">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="9bfe4-845">Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-845">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="9bfe4-846">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-846">Log scopes</span></span>

 <span data-ttu-id="9bfe4-847">*Kapsam,* bir dizi mantıksal işlemi gruplayabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-847">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="9bfe4-848">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-848">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="9bfe4-849">Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-849">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="9bfe4-850">Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-850">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="9bfe4-851">Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-851">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="9bfe4-852">Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-852">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="9bfe4-853">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-853">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="9bfe4-854">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-854">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="9bfe4-855">Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-855">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="9bfe4-856">Her günlük iletisi kapsamlı bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-856">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="9bfe4-857">Yerleşik günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-857">Built-in logging providers</span></span>

<span data-ttu-id="9bfe4-858">ASP.NET Core aşağıdaki sağlayıcıları gemiler:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-858">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="9bfe4-859">Konsol</span><span class="sxs-lookup"><span data-stu-id="9bfe4-859">Console</span></span>](#console-provider)
* [<span data-ttu-id="9bfe4-860">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9bfe4-860">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="9bfe4-861">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-861">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="9bfe4-862">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9bfe4-862">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="9bfe4-863">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9bfe4-863">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="9bfe4-864">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9bfe4-864">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9bfe4-865">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9bfe4-865">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9bfe4-866">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9bfe4-866">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="9bfe4-867">ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis></span><span class="sxs-lookup"><span data-stu-id="9bfe4-867">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="9bfe4-868">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-868">Console provider</span></span>

<span data-ttu-id="9bfe4-869">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-869">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="9bfe4-870">Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-870">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="9bfe4-871">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-871">Debug provider</span></span>

<span data-ttu-id="9bfe4-872">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-872">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="9bfe4-873">Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-873">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="9bfe4-874">Olay Kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-874">Event Source provider</span></span>

<span data-ttu-id="9bfe4-875">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-875">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="9bfe4-876">Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-876">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="9bfe4-877">Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-877">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="9bfe4-878">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-878">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="9bfe4-879">ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-879">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="9bfe4-880">PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-880">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="9bfe4-881">(Dize başında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-881">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="9bfe4-883">Windows EventLog sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-883">Windows EventLog provider</span></span>

<span data-ttu-id="9bfe4-884">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-884">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="9bfe4-885">[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-885">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="9bfe4-886">`null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-886">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="9bfe4-887">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="9bfe4-887">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="9bfe4-888">`SourceName`&ndash; ".NET Çalışma Zamanı"</span><span class="sxs-lookup"><span data-stu-id="9bfe4-888">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="9bfe4-889">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="9bfe4-889">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="9bfe4-890">Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-890">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="9bfe4-891">Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-891">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="9bfe4-892">Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-892">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="9bfe4-893">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-893">TraceSource provider</span></span>

<span data-ttu-id="9bfe4-894">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-894">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="9bfe4-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="9bfe4-896">Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-896">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="9bfe4-897">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-897">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="9bfe4-898">Azure Uygulama Hizmeti sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-898">Azure App Service provider</span></span>

<span data-ttu-id="9bfe4-899">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-899">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="9bfe4-900">Sağlayıcı paketi [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-900">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9bfe4-901">.NET Framework'u hedef alırken `Microsoft.AspNetCore.App` veya meta pakete başvururken, sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-901">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="9bfe4-902">Aşırı <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> yükleme içeri geçmeni <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>sağlar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-902">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="9bfe4-903">Ayarlar nesnesi, günlüğe kaydetme çıktısı şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-903">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="9bfe4-904">(*Çıktı şablonu,* bir yöntem çağrısıyla sağlanana ek olarak tüm `ILogger` günlüklere uygulanan bir ileti şablonudur.)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-904">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="9bfe4-905">Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-905">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="9bfe4-906">Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-906">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="9bfe4-907">**Uygulama Günlüğü (Filesystem)**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-907">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="9bfe4-908">**Uygulama Günlüğü (Blob)**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-908">**Application Logging (Blob)**</span></span>

<span data-ttu-id="9bfe4-909">Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-909">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="9bfe4-910">Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-910">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="9bfe4-911">Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-911">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="9bfe4-912">Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-912">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="9bfe4-913">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-913">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="9bfe4-914">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="9bfe4-914">Azure log streaming</span></span>

<span data-ttu-id="9bfe4-915">Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-915">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="9bfe4-916">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-916">The app server</span></span>
* <span data-ttu-id="9bfe4-917">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="9bfe4-917">The web server</span></span>
* <span data-ttu-id="9bfe4-918">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="9bfe4-918">Failed request tracing</span></span>

<span data-ttu-id="9bfe4-919">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-919">To configure Azure log streaming:</span></span>

* <span data-ttu-id="9bfe4-920">Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-920">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="9bfe4-921">**Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**</span><span class="sxs-lookup"><span data-stu-id="9bfe4-921">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="9bfe4-922">Günlük **Düzeyi'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-922">Choose the log **Level**.</span></span> <span data-ttu-id="9bfe4-923">Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-923">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="9bfe4-924">Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-924">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="9bfe4-925">Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-925">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="9bfe4-926">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="9bfe4-926">Azure Application Insights trace logging</span></span>

<span data-ttu-id="9bfe4-927">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-927">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="9bfe4-928">Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-928">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="9bfe4-929">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-929">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="9bfe4-930">Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-930">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="9bfe4-931">Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-931">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="9bfe4-932">ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-932">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="9bfe4-933">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-933">For more information, see the following resources:</span></span>

* [<span data-ttu-id="9bfe4-934">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="9bfe4-934">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="9bfe4-935">[ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-935">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="9bfe4-936">[.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="9bfe4-937">[Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="9bfe4-937">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="9bfe4-938">Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)</span><span class="sxs-lookup"><span data-stu-id="9bfe4-938">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="9bfe4-939">Üçüncü taraf günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="9bfe4-939">Third-party logging providers</span></span>

<span data-ttu-id="9bfe4-940">ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-940">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="9bfe4-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="9bfe4-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="9bfe4-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="9bfe4-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="9bfe4-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="9bfe4-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="9bfe4-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="9bfe4-948">[Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-948">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="9bfe4-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="9bfe4-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="9bfe4-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="9bfe4-951">Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-951">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9bfe4-952">Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="9bfe4-952">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="9bfe4-953">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-953">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="9bfe4-954">Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-954">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="9bfe4-955">Daha fazla bilgi için her sağlayıcının belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-955">For more information, see each provider's documentation.</span></span> <span data-ttu-id="9bfe4-956">Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9bfe4-956">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bfe4-957">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9bfe4-957">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
