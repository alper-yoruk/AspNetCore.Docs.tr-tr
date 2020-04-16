---
title: .NET Core ve ASP.NET Core'da Oturum Açma
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet paketi tarafından sağlanan günlük çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440746"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="cfbb4-103">.NET Core ve ASP.NET Core'da Oturum Açma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cfbb4-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cfbb4-105">.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="cfbb4-106">Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="cfbb4-107">Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalarındandır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="cfbb4-108">Bu kod parçacıklarının günlüğe özgü [bölümleri, Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanan herhangi bir .NET Core uygulaması için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="cfbb4-109">Web konsolu olmayan bir uygulamada Genel Ana Bilgisayar'ın nasıl kullanılacağına bir örnek olarak, [Arka Plan Görevleri örnek uygulamasının](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) <xref:fundamentals/host/hosted-services> *Program.cs* dosyasına bakın .</span><span class="sxs-lookup"><span data-stu-id="cfbb4-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="cfbb4-110">Genel Ana Bilgisayar olmayan uygulamaların günlük [kodu, sağlayıcıların eklenme](#add-providers) ve [kaydedicilerin oluşturulma](#create-logs)şeklinde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="cfbb4-111">Ana bilgisayar kodu örnekleri makalenin bu bölümlerinde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="cfbb4-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="cfbb4-113">Sağlayıcılar ekleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-113">Add providers</span></span>

<span data-ttu-id="cfbb4-114">Bir günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="cfbb4-115">Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="cfbb4-116">Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="cfbb4-117">Genel Ana Bilgisayar kullanan bir uygulamaya sağlayıcı eklemek `Add{provider name}` *için, sağlayıcının*uzantı yöntemini Program.cs:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="cfbb4-118">Barındırılamayan bir konsol uygulamasında, `Add{provider name}` sağlayıcının uzatma `LoggerFactory`yöntemini arayarak aşağıdakileri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="cfbb4-119">`LoggerFactory`ve `AddConsole` için `using` `Microsoft.Extensions.Logging`bir ifade gerektirir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="cfbb4-120">Varsayılan ASP.NET Core proje <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>şablonları çağrı , aşağıdaki günlük sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="cfbb4-121">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="cfbb4-122">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="cfbb4-123">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="cfbb4-124">[EventLog](#windows-eventlog-provider) (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="cfbb4-125">Varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="cfbb4-126">Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="cfbb4-127">Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="cfbb4-128">Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-128">Create logs</span></span>

<span data-ttu-id="cfbb4-129">Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="cfbb4-130">Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="cfbb4-131">Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .</span><span class="sxs-lookup"><span data-stu-id="cfbb4-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="cfbb4-132">Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="cfbb4-133">Günlük *kategorisi,* her günlükle ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="cfbb4-134">DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="cfbb4-135">Aşağıdaki ana bilgisayar olmayan konsol uygulaması örneği, `LoggingConsoleApp.Program` kategori olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="cfbb4-136">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="cfbb4-137">Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="cfbb4-138">[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="cfbb4-139">Program sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-139">Create logs in the Program class</span></span>

<span data-ttu-id="cfbb4-140">ASP.NET Core `Program` uygulamasının sınıfına günlük yazmak için, ana bilgisayarı yaptıktan sonra DI'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="cfbb4-141">Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="cfbb4-142">Ancak, ayrı bir logger kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-142">However, a separate logger can be used.</span></span> <span data-ttu-id="cfbb4-143">Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateHostBuilder`açmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="cfbb4-144">`AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="cfbb4-145">Başlangıç sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-145">Create logs in the Startup class</span></span>

<span data-ttu-id="cfbb4-146">ASP.NET Core uygulaması `Startup.Configure` nın yönteminde günlük yazmak `ILogger` için yöntem imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="cfbb4-147">`Startup.ConfigureServices` Yöntemdeki DI kapsayıcı kurulumu tamamlanmadan önce günlükleri yazma desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="cfbb4-148">`Startup` Logger enjeksiyon yapıcı içine desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="cfbb4-149">Yöntem imzasına `Startup.ConfigureServices` logger enjeksiyonu desteklenmez</span><span class="sxs-lookup"><span data-stu-id="cfbb4-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="cfbb4-150">Bu kısıtlamanın nedeni, günlüğe kaydetmenin DI'ye ve yapılandırmaya bağlı olmasıdır, bu da sırayla DI'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="cfbb4-151">DI kapsayıcısı bitene kadar `ConfigureServices` kurulmadı.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="cfbb4-152">Web Barındırıcısı için `Startup` ayrı bir DI kapsayıcı oluşturulduğundan, ASP.NET Core'un önceki sürümlerinde bir logger'ın oluşturucu enjeksiyonu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="cfbb4-153">Genel Ana Bilgisayar için neden yalnızca bir kapsayıcı oluşturulduğu hakkında bilgi [için, kesme değişikliği duyurusuna](https://github.com/aspnet/Announcements/issues/353)bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="cfbb4-154">Bağlı bir hizmeti yapılandırmanız `ILogger<T>`gerekiyorsa, bunu yine de yapıcı enjeksiyon kullanarak veya bir fabrika yöntemi sağlayarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="cfbb4-155">Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="cfbb4-156">Örneğin, bir mülkü DI'den bir hizmetle doldurmanız gerektiğini varsayalım:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="cfbb4-157">Önceki vurgulanan kod, `Func` DI kapsayıcısının bir örneğini oluşturmak için `MyService`ilk kez çalışan bir koddur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="cfbb4-158">Kayıtlı hizmetlerden herhangi biri bu şekilde erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="cfbb4-159">Asynchronous logger yöntemleri yok</span><span class="sxs-lookup"><span data-stu-id="cfbb4-159">No asynchronous logger methods</span></span>

<span data-ttu-id="cfbb4-160">Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="cfbb4-161">Günlük veri deponuz yavaşsa, doğrudan yazmayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="cfbb4-162">Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="cfbb4-163">Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="cfbb4-164">Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="cfbb4-165">Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="cfbb4-166">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-166">Configuration</span></span>

<span data-ttu-id="cfbb4-167">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="cfbb4-168">Dosya biçimleri (INI, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="cfbb4-169">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-169">Command-line arguments.</span></span>
* <span data-ttu-id="cfbb4-170">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-170">Environment variables.</span></span>
* <span data-ttu-id="cfbb4-171">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="cfbb4-172">Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="cfbb4-173">[Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="cfbb4-174">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="cfbb4-175">Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="cfbb4-176">Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="cfbb4-177">Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="cfbb4-178">Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="cfbb4-179">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="cfbb4-180">Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="cfbb4-181">Örnek Konsol sağlayıcısı içindir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-181">The example is for the Console provider.</span></span> <span data-ttu-id="cfbb4-182">Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="cfbb4-183">Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="cfbb4-184">`LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="cfbb4-185">Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="cfbb4-186">Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="cfbb4-187">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="cfbb4-188">Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="cfbb4-189">Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="cfbb4-190">Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="cfbb4-191">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-191">Sample logging output</span></span>

<span data-ttu-id="cfbb4-192">Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="cfbb4-193">Konsol çıkışına bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-193">Here's an example of console output:</span></span>

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

<span data-ttu-id="cfbb4-194">Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="cfbb4-195">Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="cfbb4-196">Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApiSample" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="cfbb4-197">"Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="cfbb4-198">ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="cfbb4-199">Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="cfbb4-200">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-200">NuGet packages</span></span>

<span data-ttu-id="cfbb4-201">Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="cfbb4-202">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-202">Log category</span></span>

<span data-ttu-id="cfbb4-203">Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="cfbb4-204">Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="cfbb4-205">Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="cfbb4-206">Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="cfbb4-207">Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="cfbb4-208">`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="cfbb4-209">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-209">Log level</span></span>

<span data-ttu-id="cfbb4-210">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="cfbb4-211">Günlük düzeyi önem veya önemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="cfbb4-212">Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="cfbb4-213">Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="cfbb4-214">Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="cfbb4-215">İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="cfbb4-216">Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="cfbb4-217">Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="cfbb4-218">Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="cfbb4-219">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="cfbb4-220">Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="cfbb4-221">ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="cfbb4-222">İz = 0</span><span class="sxs-lookup"><span data-stu-id="cfbb4-222">Trace = 0</span></span>

  <span data-ttu-id="cfbb4-223">Genellikle yalnızca hata ayıklama için değerli olan bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="cfbb4-224">Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="cfbb4-225">*Varsayılan olarak devre dışı bırakılır.*</span><span class="sxs-lookup"><span data-stu-id="cfbb4-225">*Disabled by default.*</span></span>

* <span data-ttu-id="cfbb4-226">Hata Ayıklama = 1</span><span class="sxs-lookup"><span data-stu-id="cfbb4-226">Debug = 1</span></span>

  <span data-ttu-id="cfbb4-227">Geliştirme ve hata ayıklama yararlı olabilir bilgi için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="cfbb4-228">Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="cfbb4-229">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="cfbb4-229">Information = 2</span></span>

  <span data-ttu-id="cfbb4-230">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="cfbb4-231">Bu günlüklerin genellikle bazı uzun vadeli değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="cfbb4-232">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="cfbb4-233">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="cfbb4-233">Warning = 3</span></span>

  <span data-ttu-id="cfbb4-234">Uygulama akışındaki anormal veya beklenmeyen olaylar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="cfbb4-235">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="cfbb4-236">İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="cfbb4-237">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="cfbb4-238">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="cfbb4-238">Error = 4</span></span>

  <span data-ttu-id="cfbb4-239">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="cfbb4-240">Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="cfbb4-241">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="cfbb4-242">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="cfbb4-242">Critical = 5</span></span>

  <span data-ttu-id="cfbb4-243">Acil müdahale gerektiren arızalar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-243">For failures that require immediate attention.</span></span> <span data-ttu-id="cfbb4-244">Örnekler: disk alanı dışında veri kaybı senaryoları.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="cfbb4-245">Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="cfbb4-246">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-246">For example:</span></span>

* <span data-ttu-id="cfbb4-247">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-247">In production:</span></span>
  * <span data-ttu-id="cfbb4-248">`Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="cfbb4-249">Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="cfbb4-250">Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="cfbb4-251">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="cfbb4-252">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-252">During development:</span></span>
  * <span data-ttu-id="cfbb4-253">İletileri konsola kaydedin. `Warning` `Critical`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="cfbb4-254">Sorun `Trace` `Information` giderme iletileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="cfbb4-255">Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="cfbb4-256">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="cfbb4-257">Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="cfbb4-258">Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="cfbb4-259">Günlük olay kimliği</span><span class="sxs-lookup"><span data-stu-id="cfbb4-259">Log event ID</span></span>

<span data-ttu-id="cfbb4-260">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="cfbb4-261">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="cfbb4-262">Olay kimliği, bir dizi olayı ilişkilendirer.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-262">An event ID associates a set of events.</span></span> <span data-ttu-id="cfbb4-263">Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="cfbb4-264">Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="cfbb4-265">Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="cfbb4-266">Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="cfbb4-267">Günlük ileti şablonu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-267">Log message template</span></span>

<span data-ttu-id="cfbb4-268">Her günlük bir ileti şablonu belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-268">Each log specifies a message template.</span></span> <span data-ttu-id="cfbb4-269">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="cfbb4-270">Yer tutucular için adları kullanın, sayılar için değil.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="cfbb4-271">Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="cfbb4-272">Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="cfbb4-273">Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="cfbb4-274">Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="cfbb4-275">Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="cfbb4-276">Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="cfbb4-277">Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="cfbb4-278">Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="cfbb4-279">Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="cfbb4-280">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-280">Logging exceptions</span></span>

<span data-ttu-id="cfbb4-281">Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="cfbb4-282">Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="cfbb4-283">Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="cfbb4-284">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-284">Log filtering</span></span>

<span data-ttu-id="cfbb4-285">Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="cfbb4-286">Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="cfbb4-287">Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="cfbb4-288">İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="cfbb4-289">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-289">Create filter rules in configuration</span></span>

<span data-ttu-id="cfbb4-290">Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="cfbb4-291">Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="cfbb4-292">Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="cfbb4-293">Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="cfbb4-294">Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="cfbb4-295">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-295">Filter rules in code</span></span>

<span data-ttu-id="cfbb4-296">Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="cfbb4-297">İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="cfbb4-298">İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="cfbb4-299">Filtreleme kuralları nasıl uygulanır?</span><span class="sxs-lookup"><span data-stu-id="cfbb4-299">How filtering rules are applied</span></span>

<span data-ttu-id="cfbb4-300">Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="cfbb4-301">İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="cfbb4-302">Sayı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-302">Number</span></span> | <span data-ttu-id="cfbb4-303">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-303">Provider</span></span>      | <span data-ttu-id="cfbb4-304">Ile başlayan kategoriler ...</span><span class="sxs-lookup"><span data-stu-id="cfbb4-304">Categories that begin with ...</span></span>          | <span data-ttu-id="cfbb4-305">Minimum günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="cfbb4-306">1</span><span class="sxs-lookup"><span data-stu-id="cfbb4-306">1</span></span>      | <span data-ttu-id="cfbb4-307">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-307">Debug</span></span>         | <span data-ttu-id="cfbb4-308">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="cfbb4-308">All categories</span></span>                          | <span data-ttu-id="cfbb4-309">Bilgi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-309">Information</span></span>       |
| <span data-ttu-id="cfbb4-310">2</span><span class="sxs-lookup"><span data-stu-id="cfbb4-310">2</span></span>      | <span data-ttu-id="cfbb4-311">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-311">Console</span></span>       | <span data-ttu-id="cfbb4-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="cfbb4-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="cfbb4-313">Uyarı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-313">Warning</span></span>           |
| <span data-ttu-id="cfbb4-314">3</span><span class="sxs-lookup"><span data-stu-id="cfbb4-314">3</span></span>      | <span data-ttu-id="cfbb4-315">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-315">Console</span></span>       | <span data-ttu-id="cfbb4-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="cfbb4-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="cfbb4-317">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-317">Debug</span></span>             |
| <span data-ttu-id="cfbb4-318">4</span><span class="sxs-lookup"><span data-stu-id="cfbb4-318">4</span></span>      | <span data-ttu-id="cfbb4-319">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-319">Console</span></span>       | <span data-ttu-id="cfbb4-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="cfbb4-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="cfbb4-321">Hata</span><span class="sxs-lookup"><span data-stu-id="cfbb4-321">Error</span></span>             |
| <span data-ttu-id="cfbb4-322">5</span><span class="sxs-lookup"><span data-stu-id="cfbb4-322">5</span></span>      | <span data-ttu-id="cfbb4-323">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-323">Console</span></span>       | <span data-ttu-id="cfbb4-324">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="cfbb4-324">All categories</span></span>                          | <span data-ttu-id="cfbb4-325">Bilgi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-325">Information</span></span>       |
| <span data-ttu-id="cfbb4-326">6</span><span class="sxs-lookup"><span data-stu-id="cfbb4-326">6</span></span>      | <span data-ttu-id="cfbb4-327">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-327">All providers</span></span> | <span data-ttu-id="cfbb4-328">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="cfbb4-328">All categories</span></span>                          | <span data-ttu-id="cfbb4-329">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-329">Debug</span></span>             |
| <span data-ttu-id="cfbb4-330">7</span><span class="sxs-lookup"><span data-stu-id="cfbb4-330">7</span></span>      | <span data-ttu-id="cfbb4-331">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-331">All providers</span></span> | <span data-ttu-id="cfbb4-332">Sistem</span><span class="sxs-lookup"><span data-stu-id="cfbb4-332">System</span></span>                                  | <span data-ttu-id="cfbb4-333">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-333">Debug</span></span>             |
| <span data-ttu-id="cfbb4-334">8</span><span class="sxs-lookup"><span data-stu-id="cfbb4-334">8</span></span>      | <span data-ttu-id="cfbb4-335">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-335">Debug</span></span>         | <span data-ttu-id="cfbb4-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="cfbb4-336">Microsoft</span></span>                               | <span data-ttu-id="cfbb4-337">İzleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-337">Trace</span></span>             |

<span data-ttu-id="cfbb4-338">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="cfbb4-339">Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="cfbb4-340">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="cfbb4-341">Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="cfbb4-342">Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="cfbb4-343">Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="cfbb4-344">Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="cfbb4-345">Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="cfbb4-346">Birden çok kural seçilirse, **sonuncusunu** alın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="cfbb4-347">Kural seçili değilse, `MinimumLevel`kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="cfbb4-348">Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="cfbb4-349">Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="cfbb4-350">Kural 8 çok özeldir, bu yüzden seçilen kuraldır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="cfbb4-351">Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="cfbb4-352">Kural 3 çok özeldir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="cfbb4-353">Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="cfbb4-354">`Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="cfbb4-355">Sağlayıcı diğer adlar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-355">Provider aliases</span></span>

<span data-ttu-id="cfbb4-356">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="cfbb4-357">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="cfbb4-358">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-358">Console</span></span>
* <span data-ttu-id="cfbb4-359">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-359">Debug</span></span>
* <span data-ttu-id="cfbb4-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="cfbb4-360">EventSource</span></span>
* <span data-ttu-id="cfbb4-361">Eventlog</span><span class="sxs-lookup"><span data-stu-id="cfbb4-361">EventLog</span></span>
* <span data-ttu-id="cfbb4-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="cfbb4-362">TraceSource</span></span>
* <span data-ttu-id="cfbb4-363">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="cfbb4-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="cfbb4-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="cfbb4-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="cfbb4-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="cfbb4-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="cfbb4-366">Varsayılan minimum düzey</span><span class="sxs-lookup"><span data-stu-id="cfbb4-366">Default minimum level</span></span>

<span data-ttu-id="cfbb4-367">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="cfbb4-368">Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="cfbb4-369">Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="cfbb4-370">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-370">Filter functions</span></span>

<span data-ttu-id="cfbb4-371">Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="cfbb4-372">İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="cfbb4-373">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="cfbb4-374">Sistem kategorileri ve düzeyleri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-374">System categories and levels</span></span>

<span data-ttu-id="cfbb4-375">ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="cfbb4-376">Kategori</span><span class="sxs-lookup"><span data-stu-id="cfbb4-376">Category</span></span>                            | <span data-ttu-id="cfbb4-377">Notlar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="cfbb4-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="cfbb4-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="cfbb4-379">General ASP.NET Core teşhisi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="cfbb4-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="cfbb4-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="cfbb4-381">Hangi anahtarlar düşünüldü, bulundu ve kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="cfbb4-382">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="cfbb4-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="cfbb4-383">Ev sahiplerine izin verildi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-383">Hosts allowed.</span></span> |
| <span data-ttu-id="cfbb4-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="cfbb4-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="cfbb4-385">HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="cfbb4-386">Hangi barındırma başlangıç meclisleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="cfbb4-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="cfbb4-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="cfbb4-388">MVC ve Razor teşhis.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="cfbb4-389">Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="cfbb4-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="cfbb4-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="cfbb4-391">Rota eşleştirme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-391">Route matching information.</span></span> |
| <span data-ttu-id="cfbb4-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="cfbb4-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="cfbb4-393">Bağlantı başlatın, durdurun ve yanıtları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="cfbb4-394">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="cfbb4-395">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="cfbb4-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="cfbb4-396">Dosyalar servis edilebis.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-396">Files served.</span></span> |
| <span data-ttu-id="cfbb4-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="cfbb4-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="cfbb4-398">Genel Varlık Çerçeve Çekirdek tanılama.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="cfbb4-399">Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="cfbb4-400">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-400">Log scopes</span></span>

 <span data-ttu-id="cfbb4-401">*Kapsam,* bir dizi mantıksal işlemi gruplayabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="cfbb4-402">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="cfbb4-403">Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="cfbb4-404">Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="cfbb4-405">Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="cfbb4-406">Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="cfbb4-407">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="cfbb4-408">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="cfbb4-409">Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="cfbb4-410">Her günlük iletisi kapsamlı bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="cfbb4-411">Yerleşik günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-411">Built-in logging providers</span></span>

<span data-ttu-id="cfbb4-412">ASP.NET Core aşağıdaki sağlayıcıları gemiler:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="cfbb4-413">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="cfbb4-414">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="cfbb4-415">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="cfbb4-416">Eventlog</span><span class="sxs-lookup"><span data-stu-id="cfbb4-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="cfbb4-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="cfbb4-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="cfbb4-418">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="cfbb4-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="cfbb4-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="cfbb4-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="cfbb4-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="cfbb4-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="cfbb4-421">ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis></span><span class="sxs-lookup"><span data-stu-id="cfbb4-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="cfbb4-422">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-422">Console provider</span></span>

<span data-ttu-id="cfbb4-423">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="cfbb4-424">Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="cfbb4-425">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-425">Debug provider</span></span>

<span data-ttu-id="cfbb4-426">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="cfbb4-427">Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="cfbb4-428">Olay Kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-428">Event Source provider</span></span>

<span data-ttu-id="cfbb4-429">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="cfbb4-430">Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="cfbb4-431">Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="cfbb4-432">dotnet izleme takım</span><span class="sxs-lookup"><span data-stu-id="cfbb4-432">dotnet trace tooling</span></span>

<span data-ttu-id="cfbb4-433">[Dotnet izleme](/dotnet/core/diagnostics/dotnet-trace) aracı, çalışan bir işlemin .NET Core izlerinin toplanmasını sağlayan bir çapraz platform CLI global aracıdır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="cfbb4-434">Araç <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verilerini bir <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="cfbb4-435">Aşağıdaki komutla dotnet izleme aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="cfbb4-436">Bir uygulamadan iz toplamak için dotnet izleme aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="cfbb4-437">Uygulama ana bilgisayarı `CreateDefaultBuilder`oluşturmuyorsa, Olay Kaynağı [sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="cfbb4-438">Uygulamayı komutla `dotnet run` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="cfbb4-439">.NET Core uygulamasının işlem tanımlayıcısını (PID) belirleyin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="cfbb4-440">Windows'da aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="cfbb4-441">Görev Yöneticisi (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="cfbb4-442">görev listesi komutu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="cfbb4-443">Get-Process Powershell komutu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="cfbb4-444">Linux'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="cfbb4-445">Uygulamanın derlemesi ile aynı ada sahip işlem için PID'yi bulun.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="cfbb4-446">Komutu `dotnet trace` uygulayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="cfbb4-447">Genel komut sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="cfbb4-448">PowerShell komut uyruğunu kullanırken, değeri tek tırnak içine alabilen `--providers` (`'`):</span><span class="sxs-lookup"><span data-stu-id="cfbb4-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="cfbb4-449">Windows olmayan platformlarda, `-f speedscope` çıktı izleme dosyasının biçimini `speedscope`''ye değiştirme seçeneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="cfbb4-450">Anahtar kelime</span><span class="sxs-lookup"><span data-stu-id="cfbb4-450">Keyword</span></span> | <span data-ttu-id="cfbb4-451">Açıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="cfbb4-452">1</span><span class="sxs-lookup"><span data-stu-id="cfbb4-452">1</span></span>       | <span data-ttu-id="cfbb4-453">Hakkında meta olayları `LoggingEventSource`günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="cfbb4-454">Olayları kaydetmez). `ILogger`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="cfbb4-455">2</span><span class="sxs-lookup"><span data-stu-id="cfbb4-455">2</span></span>       | <span data-ttu-id="cfbb4-456">Çağrıldığında `Message` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="cfbb4-457">Bilgileri programatik (biçimlendirilmemiş) bir şekilde sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="cfbb4-458">4</span><span class="sxs-lookup"><span data-stu-id="cfbb4-458">4</span></span>       | <span data-ttu-id="cfbb4-459">Çağrıldığında `FormatMessage` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="cfbb4-460">Bilgilerin biçimlendirilmiş dize sürümünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="cfbb4-461">8</span><span class="sxs-lookup"><span data-stu-id="cfbb4-461">8</span></span>       | <span data-ttu-id="cfbb4-462">Çağrıldığında `MessageJson` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="cfbb4-463">Bağımsız değişkenlerin JSON temsilini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="cfbb4-464">Etkinlik Düzeyi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-464">Event Level</span></span> | <span data-ttu-id="cfbb4-465">Açıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="cfbb4-466">0</span><span class="sxs-lookup"><span data-stu-id="cfbb4-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="cfbb4-467">1</span><span class="sxs-lookup"><span data-stu-id="cfbb4-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="cfbb4-468">2</span><span class="sxs-lookup"><span data-stu-id="cfbb4-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="cfbb4-469">3</span><span class="sxs-lookup"><span data-stu-id="cfbb4-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="cfbb4-470">4</span><span class="sxs-lookup"><span data-stu-id="cfbb4-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="cfbb4-471">5</span><span class="sxs-lookup"><span data-stu-id="cfbb4-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="cfbb4-472">`FilterSpecs`için `{Logger Category}` girişler ve `{Event Level}` ek günlük filtreleme koşulları temsil.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="cfbb4-473">Bir `FilterSpecs` semicolon ile`;`ayrı girişleri ( ).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="cfbb4-474">Windows komut uyruğunu kullanarak örnek `--providers` (değer etrafında tek bir tırnak işareti**yok):**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="cfbb4-475">Önceki komut etkinleştirir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-475">The preceding command activates:</span></span>

   * <span data-ttu-id="cfbb4-476">Hatalar için biçimlendirilmiş dizeleri (`4`) üretmek`2`için Olay Kaynağı kaydedici ( ).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="cfbb4-477">`Microsoft.AspNetCore.Hosting``Informational` günlük düzeyinde günlüğe`4`kaydetme ( ).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="cfbb4-478">Enter tuşuna veya Ctrl+C tuşuna basarak dotnet izleme aletini durdurun.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="cfbb4-479">İzleme, `dotnet trace` komutun yürütüldüğü klasöre *trace.nettrace* adı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="cfbb4-480">[Perfview](#perfview)ile iz açın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="cfbb4-481">*trace.nettrace* dosyasını açın ve izleme olaylarını keşfedin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="cfbb4-482">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-482">For more information, see:</span></span>

* <span data-ttu-id="cfbb4-483">[Performans analizi yardımcı programı (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core dokümantasyon) için izleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="cfbb4-484">[Performans analizi yardımcı programı (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub depo belgeleri) için izleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="cfbb4-485">[LoggingEventSource Sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Tarayıcısı)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="cfbb4-486">[LoggingEventSource referans kaynağı (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Farklı bir sürüm için referans `release/{Version}`kaynağı `{Version}` elde etmek için, şubeyi , ASP.NET Core sürümü istenilen nerede değiştirin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="cfbb4-487">[Perfview](#perfview) &ndash; Olay Kaynağı izlerini görüntülemek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="cfbb4-488">Perfview</span><span class="sxs-lookup"><span data-stu-id="cfbb4-488">Perfview</span></span>

<span data-ttu-id="cfbb4-489">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="cfbb4-490">ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="cfbb4-491">PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="cfbb4-492">(Dize başında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="cfbb4-494">Windows EventLog sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-494">Windows EventLog provider</span></span>

<span data-ttu-id="cfbb4-495">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="cfbb4-496">[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="cfbb4-497">`null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="cfbb4-498">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="cfbb4-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="cfbb4-499">`SourceName`&ndash; ".NET Çalışma Zamanı"</span><span class="sxs-lookup"><span data-stu-id="cfbb4-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="cfbb4-500">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="cfbb4-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="cfbb4-501">Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="cfbb4-502">Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="cfbb4-503">Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="cfbb4-504">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-504">TraceSource provider</span></span>

<span data-ttu-id="cfbb4-505">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="cfbb4-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="cfbb4-507">Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="cfbb4-508">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="cfbb4-509">Azure Uygulama Hizmeti sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-509">Azure App Service provider</span></span>

<span data-ttu-id="cfbb4-510">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="cfbb4-511">Sağlayıcı paketi paylaşılan çerçeveye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="cfbb4-512">Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="cfbb4-513">Sağlayıcı ayarlarını yapılandırmak <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>için aşağıdaki örnekte gösterildiği gibi kullanın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="cfbb4-514">Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="cfbb4-515">Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="cfbb4-516">**Uygulama Günlüğü (Filesystem)**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="cfbb4-517">**Uygulama Günlüğü (Blob)**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="cfbb4-518">Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="cfbb4-519">Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="cfbb4-520">Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="cfbb4-521">Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="cfbb4-522">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="cfbb4-523">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-523">Azure log streaming</span></span>

<span data-ttu-id="cfbb4-524">Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="cfbb4-525">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-525">The app server</span></span>
* <span data-ttu-id="cfbb4-526">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-526">The web server</span></span>
* <span data-ttu-id="cfbb4-527">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-527">Failed request tracing</span></span>

<span data-ttu-id="cfbb4-528">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="cfbb4-529">Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="cfbb4-530">**Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="cfbb4-531">Günlük **Düzeyi'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-531">Choose the log **Level**.</span></span> <span data-ttu-id="cfbb4-532">Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="cfbb4-533">Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="cfbb4-534">Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="cfbb4-535">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="cfbb4-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="cfbb4-536">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="cfbb4-537">Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="cfbb4-538">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="cfbb4-539">Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="cfbb4-540">Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="cfbb4-541">ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="cfbb4-542">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="cfbb4-543">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="cfbb4-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="cfbb4-544">[ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="cfbb4-545">[.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="cfbb4-546">[Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="cfbb4-547">Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="cfbb4-548">Üçüncü taraf günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-548">Third-party logging providers</span></span>

<span data-ttu-id="cfbb4-549">ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="cfbb4-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="cfbb4-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="cfbb4-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="cfbb4-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="cfbb4-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="cfbb4-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="cfbb4-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="cfbb4-557">[Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="cfbb4-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="cfbb4-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="cfbb4-560">Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="cfbb4-561">Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="cfbb4-562">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="cfbb4-563">Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="cfbb4-564">Daha fazla bilgi için her sağlayıcının belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="cfbb4-565">Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cfbb4-566">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfbb4-567">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cfbb4-568">.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="cfbb4-569">Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="cfbb4-570">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="cfbb4-571">Sağlayıcılar ekleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-571">Add providers</span></span>

<span data-ttu-id="cfbb4-572">Bir günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="cfbb4-573">Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="cfbb4-574">Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="cfbb4-575">Sağlayıcı eklemek için `Add{provider name}` *sağlayıcının*uzatma yöntemini Program.cs olarak arayın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="cfbb4-576">Önceki kod için başvurular `Microsoft.Extensions.Logging` `Microsoft.Extensions.Configuration`ve .</span><span class="sxs-lookup"><span data-stu-id="cfbb4-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="cfbb4-577">Varsayılan proje şablonu çağrıları <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, aşağıdaki günlük sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="cfbb4-578">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-578">Console</span></span>
* <span data-ttu-id="cfbb4-579">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-579">Debug</span></span>
* <span data-ttu-id="cfbb4-580">EventSource (Core 2.2ASP.NETden itibaren)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="cfbb4-581">Kullanırsanız, `CreateDefaultBuilder`varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="cfbb4-582">Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="cfbb4-583">Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="cfbb4-584">Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-584">Create logs</span></span>

<span data-ttu-id="cfbb4-585">Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="cfbb4-586">Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="cfbb4-587">Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .</span><span class="sxs-lookup"><span data-stu-id="cfbb4-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="cfbb4-588">Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="cfbb4-589">Günlük *kategorisi,* her günlükle ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="cfbb4-590">DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="cfbb4-591">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="cfbb4-592">Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="cfbb4-593">[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="cfbb4-594">Başlangıç'ta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-594">Create logs in Startup</span></span>

<span data-ttu-id="cfbb4-595">`Startup` Sınıfa günlük yazmak için, `ILogger` oluşturucu imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="cfbb4-596">Program sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-596">Create logs in the Program class</span></span>

<span data-ttu-id="cfbb4-597">`Program` Sınıfa günlük yazmak için DI'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="cfbb4-598">Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="cfbb4-599">Ancak, ayrı bir logger kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-599">However, a separate logger can be used.</span></span> <span data-ttu-id="cfbb4-600">Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateWebHostBuilder`açmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="cfbb4-601">`AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="cfbb4-602">Asynchronous logger yöntemleri yok</span><span class="sxs-lookup"><span data-stu-id="cfbb4-602">No asynchronous logger methods</span></span>

<span data-ttu-id="cfbb4-603">Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="cfbb4-604">Günlük veri deponuz yavaşsa, doğrudan yazmayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="cfbb4-605">Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="cfbb4-606">Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="cfbb4-607">Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="cfbb4-608">Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="cfbb4-609">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-609">Configuration</span></span>

<span data-ttu-id="cfbb4-610">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="cfbb4-611">Dosya biçimleri (INI, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="cfbb4-612">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-612">Command-line arguments.</span></span>
* <span data-ttu-id="cfbb4-613">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-613">Environment variables.</span></span>
* <span data-ttu-id="cfbb4-614">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="cfbb4-615">Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="cfbb4-616">[Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="cfbb4-617">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="cfbb4-618">Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="cfbb4-619">Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="cfbb4-620">Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="cfbb4-621">Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="cfbb4-622">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="cfbb4-623">Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="cfbb4-624">Örnek Konsol sağlayıcısı içindir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-624">The example is for the Console provider.</span></span> <span data-ttu-id="cfbb4-625">Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="cfbb4-626">Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="cfbb4-627">`LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="cfbb4-628">Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="cfbb4-629">Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="cfbb4-630">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="cfbb4-631">Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="cfbb4-632">Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="cfbb4-633">Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="cfbb4-634">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-634">Sample logging output</span></span>

<span data-ttu-id="cfbb4-635">Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="cfbb4-636">Konsol çıkışına bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-636">Here's an example of console output:</span></span>

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

<span data-ttu-id="cfbb4-637">Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="cfbb4-638">Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="cfbb4-639">Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApi" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="cfbb4-640">"Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="cfbb4-641">ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="cfbb4-642">Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="cfbb4-643">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-643">NuGet packages</span></span>

<span data-ttu-id="cfbb4-644">Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="cfbb4-645">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-645">Log category</span></span>

<span data-ttu-id="cfbb4-646">Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="cfbb4-647">Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="cfbb4-648">Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="cfbb4-649">Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="cfbb4-650">Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="cfbb4-651">`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="cfbb4-652">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-652">Log level</span></span>

<span data-ttu-id="cfbb4-653">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="cfbb4-654">Günlük düzeyi önem veya önemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="cfbb4-655">Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="cfbb4-656">Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="cfbb4-657">Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="cfbb4-658">İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="cfbb4-659">Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="cfbb4-660">Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="cfbb4-661">Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="cfbb4-662">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="cfbb4-663">Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="cfbb4-664">ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="cfbb4-665">İz = 0</span><span class="sxs-lookup"><span data-stu-id="cfbb4-665">Trace = 0</span></span>

  <span data-ttu-id="cfbb4-666">Genellikle yalnızca hata ayıklama için değerli olan bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="cfbb4-667">Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="cfbb4-668">*Varsayılan olarak devre dışı bırakılır.*</span><span class="sxs-lookup"><span data-stu-id="cfbb4-668">*Disabled by default.*</span></span>

* <span data-ttu-id="cfbb4-669">Hata Ayıklama = 1</span><span class="sxs-lookup"><span data-stu-id="cfbb4-669">Debug = 1</span></span>

  <span data-ttu-id="cfbb4-670">Geliştirme ve hata ayıklama yararlı olabilir bilgi için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="cfbb4-671">Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="cfbb4-672">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="cfbb4-672">Information = 2</span></span>

  <span data-ttu-id="cfbb4-673">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="cfbb4-674">Bu günlüklerin genellikle bazı uzun vadeli değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="cfbb4-675">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="cfbb4-676">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="cfbb4-676">Warning = 3</span></span>

  <span data-ttu-id="cfbb4-677">Uygulama akışındaki anormal veya beklenmeyen olaylar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="cfbb4-678">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="cfbb4-679">İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="cfbb4-680">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="cfbb4-681">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="cfbb4-681">Error = 4</span></span>

  <span data-ttu-id="cfbb4-682">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="cfbb4-683">Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="cfbb4-684">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="cfbb4-685">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="cfbb4-685">Critical = 5</span></span>

  <span data-ttu-id="cfbb4-686">Acil müdahale gerektiren arızalar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-686">For failures that require immediate attention.</span></span> <span data-ttu-id="cfbb4-687">Örnekler: disk alanı dışında veri kaybı senaryoları.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="cfbb4-688">Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="cfbb4-689">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-689">For example:</span></span>

* <span data-ttu-id="cfbb4-690">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-690">In production:</span></span>
  * <span data-ttu-id="cfbb4-691">`Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="cfbb4-692">Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="cfbb4-693">Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="cfbb4-694">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="cfbb4-695">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-695">During development:</span></span>
  * <span data-ttu-id="cfbb4-696">İletileri konsola kaydedin. `Warning` `Critical`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="cfbb4-697">Sorun `Trace` `Information` giderme iletileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="cfbb4-698">Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="cfbb4-699">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="cfbb4-700">Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="cfbb4-701">Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="cfbb4-702">Günlük olay kimliği</span><span class="sxs-lookup"><span data-stu-id="cfbb4-702">Log event ID</span></span>

<span data-ttu-id="cfbb4-703">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="cfbb4-704">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="cfbb4-705">Olay kimliği, bir dizi olayı ilişkilendirer.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-705">An event ID associates a set of events.</span></span> <span data-ttu-id="cfbb4-706">Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="cfbb4-707">Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="cfbb4-708">Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="cfbb4-709">Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="cfbb4-710">Günlük ileti şablonu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-710">Log message template</span></span>

<span data-ttu-id="cfbb4-711">Her günlük bir ileti şablonu belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-711">Each log specifies a message template.</span></span> <span data-ttu-id="cfbb4-712">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="cfbb4-713">Yer tutucular için adları kullanın, sayılar için değil.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="cfbb4-714">Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="cfbb4-715">Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="cfbb4-716">Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="cfbb4-717">Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="cfbb4-718">Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="cfbb4-719">Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="cfbb4-720">Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="cfbb4-721">Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="cfbb4-722">Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="cfbb4-723">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-723">Logging exceptions</span></span>

<span data-ttu-id="cfbb4-724">Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="cfbb4-725">Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="cfbb4-726">Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="cfbb4-727">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-727">Log filtering</span></span>

<span data-ttu-id="cfbb4-728">Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="cfbb4-729">Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="cfbb4-730">Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="cfbb4-731">İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="cfbb4-732">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="cfbb4-732">Create filter rules in configuration</span></span>

<span data-ttu-id="cfbb4-733">Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="cfbb4-734">Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="cfbb4-735">Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="cfbb4-736">Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="cfbb4-737">Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="cfbb4-738">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-738">Filter rules in code</span></span>

<span data-ttu-id="cfbb4-739">Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="cfbb4-740">İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="cfbb4-741">İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="cfbb4-742">Filtreleme kuralları nasıl uygulanır?</span><span class="sxs-lookup"><span data-stu-id="cfbb4-742">How filtering rules are applied</span></span>

<span data-ttu-id="cfbb4-743">Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="cfbb4-744">İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="cfbb4-745">Sayı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-745">Number</span></span> | <span data-ttu-id="cfbb4-746">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-746">Provider</span></span>      | <span data-ttu-id="cfbb4-747">Ile başlayan kategoriler ...</span><span class="sxs-lookup"><span data-stu-id="cfbb4-747">Categories that begin with ...</span></span>          | <span data-ttu-id="cfbb4-748">Minimum günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="cfbb4-749">1</span><span class="sxs-lookup"><span data-stu-id="cfbb4-749">1</span></span>      | <span data-ttu-id="cfbb4-750">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-750">Debug</span></span>         | <span data-ttu-id="cfbb4-751">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="cfbb4-751">All categories</span></span>                          | <span data-ttu-id="cfbb4-752">Bilgi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-752">Information</span></span>       |
| <span data-ttu-id="cfbb4-753">2</span><span class="sxs-lookup"><span data-stu-id="cfbb4-753">2</span></span>      | <span data-ttu-id="cfbb4-754">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-754">Console</span></span>       | <span data-ttu-id="cfbb4-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="cfbb4-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="cfbb4-756">Uyarı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-756">Warning</span></span>           |
| <span data-ttu-id="cfbb4-757">3</span><span class="sxs-lookup"><span data-stu-id="cfbb4-757">3</span></span>      | <span data-ttu-id="cfbb4-758">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-758">Console</span></span>       | <span data-ttu-id="cfbb4-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="cfbb4-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="cfbb4-760">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-760">Debug</span></span>             |
| <span data-ttu-id="cfbb4-761">4</span><span class="sxs-lookup"><span data-stu-id="cfbb4-761">4</span></span>      | <span data-ttu-id="cfbb4-762">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-762">Console</span></span>       | <span data-ttu-id="cfbb4-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="cfbb4-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="cfbb4-764">Hata</span><span class="sxs-lookup"><span data-stu-id="cfbb4-764">Error</span></span>             |
| <span data-ttu-id="cfbb4-765">5</span><span class="sxs-lookup"><span data-stu-id="cfbb4-765">5</span></span>      | <span data-ttu-id="cfbb4-766">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-766">Console</span></span>       | <span data-ttu-id="cfbb4-767">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="cfbb4-767">All categories</span></span>                          | <span data-ttu-id="cfbb4-768">Bilgi</span><span class="sxs-lookup"><span data-stu-id="cfbb4-768">Information</span></span>       |
| <span data-ttu-id="cfbb4-769">6</span><span class="sxs-lookup"><span data-stu-id="cfbb4-769">6</span></span>      | <span data-ttu-id="cfbb4-770">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-770">All providers</span></span> | <span data-ttu-id="cfbb4-771">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="cfbb4-771">All categories</span></span>                          | <span data-ttu-id="cfbb4-772">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-772">Debug</span></span>             |
| <span data-ttu-id="cfbb4-773">7</span><span class="sxs-lookup"><span data-stu-id="cfbb4-773">7</span></span>      | <span data-ttu-id="cfbb4-774">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-774">All providers</span></span> | <span data-ttu-id="cfbb4-775">Sistem</span><span class="sxs-lookup"><span data-stu-id="cfbb4-775">System</span></span>                                  | <span data-ttu-id="cfbb4-776">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-776">Debug</span></span>             |
| <span data-ttu-id="cfbb4-777">8</span><span class="sxs-lookup"><span data-stu-id="cfbb4-777">8</span></span>      | <span data-ttu-id="cfbb4-778">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-778">Debug</span></span>         | <span data-ttu-id="cfbb4-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="cfbb4-779">Microsoft</span></span>                               | <span data-ttu-id="cfbb4-780">İzleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-780">Trace</span></span>             |

<span data-ttu-id="cfbb4-781">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="cfbb4-782">Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="cfbb4-783">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="cfbb4-784">Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="cfbb4-785">Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="cfbb4-786">Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="cfbb4-787">Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="cfbb4-788">Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="cfbb4-789">Birden çok kural seçilirse, **sonuncusunu** alın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="cfbb4-790">Kural seçili değilse, `MinimumLevel`kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="cfbb4-791">Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="cfbb4-792">Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="cfbb4-793">Kural 8 çok özeldir, bu yüzden seçilen kuraldır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="cfbb4-794">Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="cfbb4-795">Kural 3 çok özeldir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="cfbb4-796">Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="cfbb4-797">`Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="cfbb4-798">Sağlayıcı diğer adlar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-798">Provider aliases</span></span>

<span data-ttu-id="cfbb4-799">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="cfbb4-800">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="cfbb4-801">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-801">Console</span></span>
* <span data-ttu-id="cfbb4-802">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-802">Debug</span></span>
* <span data-ttu-id="cfbb4-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="cfbb4-803">EventSource</span></span>
* <span data-ttu-id="cfbb4-804">Eventlog</span><span class="sxs-lookup"><span data-stu-id="cfbb4-804">EventLog</span></span>
* <span data-ttu-id="cfbb4-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="cfbb4-805">TraceSource</span></span>
* <span data-ttu-id="cfbb4-806">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="cfbb4-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="cfbb4-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="cfbb4-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="cfbb4-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="cfbb4-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="cfbb4-809">Varsayılan minimum düzey</span><span class="sxs-lookup"><span data-stu-id="cfbb4-809">Default minimum level</span></span>

<span data-ttu-id="cfbb4-810">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="cfbb4-811">Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="cfbb4-812">Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="cfbb4-813">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-813">Filter functions</span></span>

<span data-ttu-id="cfbb4-814">Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="cfbb4-815">İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="cfbb4-816">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="cfbb4-817">Sistem kategorileri ve düzeyleri</span><span class="sxs-lookup"><span data-stu-id="cfbb4-817">System categories and levels</span></span>

<span data-ttu-id="cfbb4-818">ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="cfbb4-819">Kategori</span><span class="sxs-lookup"><span data-stu-id="cfbb4-819">Category</span></span>                            | <span data-ttu-id="cfbb4-820">Notlar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="cfbb4-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="cfbb4-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="cfbb4-822">General ASP.NET Core teşhisi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="cfbb4-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="cfbb4-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="cfbb4-824">Hangi anahtarlar düşünüldü, bulundu ve kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="cfbb4-825">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="cfbb4-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="cfbb4-826">Ev sahiplerine izin verildi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-826">Hosts allowed.</span></span> |
| <span data-ttu-id="cfbb4-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="cfbb4-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="cfbb4-828">HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="cfbb4-829">Hangi barındırma başlangıç meclisleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="cfbb4-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="cfbb4-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="cfbb4-831">MVC ve Razor teşhis.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="cfbb4-832">Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="cfbb4-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="cfbb4-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="cfbb4-834">Rota eşleştirme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-834">Route matching information.</span></span> |
| <span data-ttu-id="cfbb4-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="cfbb4-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="cfbb4-836">Bağlantı başlatın, durdurun ve yanıtları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="cfbb4-837">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="cfbb4-838">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="cfbb4-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="cfbb4-839">Dosyalar servis edilebis.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-839">Files served.</span></span> |
| <span data-ttu-id="cfbb4-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="cfbb4-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="cfbb4-841">Genel Varlık Çerçeve Çekirdek tanılama.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="cfbb4-842">Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="cfbb4-843">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-843">Log scopes</span></span>

 <span data-ttu-id="cfbb4-844">*Kapsam,* bir dizi mantıksal işlemi gruplayabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="cfbb4-845">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="cfbb4-846">Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="cfbb4-847">Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="cfbb4-848">Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="cfbb4-849">Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="cfbb4-850">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="cfbb4-851">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="cfbb4-852">Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="cfbb4-853">Her günlük iletisi kapsamlı bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="cfbb4-854">Yerleşik günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-854">Built-in logging providers</span></span>

<span data-ttu-id="cfbb4-855">ASP.NET Core aşağıdaki sağlayıcıları gemiler:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="cfbb4-856">Konsol</span><span class="sxs-lookup"><span data-stu-id="cfbb4-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="cfbb4-857">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="cfbb4-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="cfbb4-858">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="cfbb4-859">Eventlog</span><span class="sxs-lookup"><span data-stu-id="cfbb4-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="cfbb4-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="cfbb4-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="cfbb4-861">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="cfbb4-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="cfbb4-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="cfbb4-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="cfbb4-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="cfbb4-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="cfbb4-864">ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis></span><span class="sxs-lookup"><span data-stu-id="cfbb4-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="cfbb4-865">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-865">Console provider</span></span>

<span data-ttu-id="cfbb4-866">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="cfbb4-867">Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="cfbb4-868">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-868">Debug provider</span></span>

<span data-ttu-id="cfbb4-869">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="cfbb4-870">Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="cfbb4-871">Olay Kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-871">Event Source provider</span></span>

<span data-ttu-id="cfbb4-872">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="cfbb4-873">Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="cfbb4-874">Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="cfbb4-875">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="cfbb4-876">ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="cfbb4-877">PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="cfbb4-878">(Dize başında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="cfbb4-880">Windows EventLog sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-880">Windows EventLog provider</span></span>

<span data-ttu-id="cfbb4-881">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="cfbb4-882">[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="cfbb4-883">`null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="cfbb4-884">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="cfbb4-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="cfbb4-885">`SourceName`&ndash; ".NET Çalışma Zamanı"</span><span class="sxs-lookup"><span data-stu-id="cfbb4-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="cfbb4-886">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="cfbb4-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="cfbb4-887">Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="cfbb4-888">Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="cfbb4-889">Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="cfbb4-890">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-890">TraceSource provider</span></span>

<span data-ttu-id="cfbb4-891">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="cfbb4-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="cfbb4-893">Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="cfbb4-894">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="cfbb4-895">Azure Uygulama Hizmeti sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-895">Azure App Service provider</span></span>

<span data-ttu-id="cfbb4-896">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="cfbb4-897">Sağlayıcı paketi [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="cfbb4-898">.NET Framework'u hedef alırken `Microsoft.AspNetCore.App` veya meta pakete başvururken, sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="cfbb4-899">Aşırı <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> yükleme içeri geçmeni <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>sağlar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="cfbb4-900">Ayarlar nesnesi, günlüğe kaydetme çıktısı şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="cfbb4-901">(*Çıktı şablonu,* bir yöntem çağrısıyla sağlanana ek olarak tüm `ILogger` günlüklere uygulanan bir ileti şablonudur.)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="cfbb4-902">Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="cfbb4-903">Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="cfbb4-904">**Uygulama Günlüğü (Filesystem)**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="cfbb4-905">**Uygulama Günlüğü (Blob)**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="cfbb4-906">Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="cfbb4-907">Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="cfbb4-908">Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="cfbb4-909">Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="cfbb4-910">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="cfbb4-911">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="cfbb4-911">Azure log streaming</span></span>

<span data-ttu-id="cfbb4-912">Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="cfbb4-913">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-913">The app server</span></span>
* <span data-ttu-id="cfbb4-914">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="cfbb4-914">The web server</span></span>
* <span data-ttu-id="cfbb4-915">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="cfbb4-915">Failed request tracing</span></span>

<span data-ttu-id="cfbb4-916">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="cfbb4-917">Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="cfbb4-918">**Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**</span><span class="sxs-lookup"><span data-stu-id="cfbb4-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="cfbb4-919">Günlük **Düzeyi'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-919">Choose the log **Level**.</span></span> <span data-ttu-id="cfbb4-920">Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="cfbb4-921">Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="cfbb4-922">Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="cfbb4-923">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="cfbb4-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="cfbb4-924">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="cfbb4-925">Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="cfbb4-926">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="cfbb4-927">Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="cfbb4-928">Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="cfbb4-929">ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="cfbb4-930">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="cfbb4-931">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="cfbb4-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="cfbb4-932">[ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="cfbb4-933">[.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="cfbb4-934">[Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="cfbb4-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="cfbb4-935">Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="cfbb4-936">Üçüncü taraf günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="cfbb4-936">Third-party logging providers</span></span>

<span data-ttu-id="cfbb4-937">ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="cfbb4-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="cfbb4-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="cfbb4-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="cfbb4-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="cfbb4-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="cfbb4-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="cfbb4-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="cfbb4-945">[Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="cfbb4-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="cfbb4-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="cfbb4-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="cfbb4-948">Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="cfbb4-949">Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="cfbb4-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="cfbb4-950">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="cfbb4-951">Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="cfbb4-952">Daha fazla bilgi için her sağlayıcının belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="cfbb4-953">Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cfbb4-954">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cfbb4-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
