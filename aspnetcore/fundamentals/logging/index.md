---
title: .NET Core ve ASP.NET Core'da Oturum Açma
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet paketi tarafından sağlanan günlük çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663305"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="c6ec4-103">.NET Core ve ASP.NET Core'da Oturum Açma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="c6ec4-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c6ec4-105">.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="c6ec4-106">Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6ec4-107">Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalarındandır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="c6ec4-108">Bu kod parçacıklarının günlüğe özgü [bölümleri, Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanan herhangi bir .NET Core uygulaması için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="c6ec4-109">Web konsolu olmayan bir uygulamada Genel Ana Bilgisayar'ın nasıl kullanılacağına bir örnek olarak, [Arka Plan Görevleri örnek uygulamasının](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) <xref:fundamentals/host/hosted-services> *Program.cs* dosyasına bakın .</span><span class="sxs-lookup"><span data-stu-id="c6ec4-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="c6ec4-110">Genel Ana Bilgisayar olmayan uygulamaların günlük [kodu, sağlayıcıların eklenme](#add-providers) ve [kaydedicilerin oluşturulma](#create-logs)şeklinde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="c6ec4-111">Ana bilgisayar kodu örnekleri makalenin bu bölümlerinde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="c6ec4-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="c6ec4-113">Sağlayıcılar ekleme</span><span class="sxs-lookup"><span data-stu-id="c6ec4-113">Add providers</span></span>

<span data-ttu-id="c6ec4-114">Bir günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="c6ec4-115">Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="c6ec4-116">Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6ec4-117">Genel Ana Bilgisayar kullanan bir uygulamaya sağlayıcı eklemek `Add{provider name}` *için, sağlayıcının*uzantı yöntemini Program.cs:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="c6ec4-118">Barındırılamayan bir konsol uygulamasında, `Add{provider name}` sağlayıcının uzatma `LoggerFactory`yöntemini arayarak aşağıdakileri</span><span class="sxs-lookup"><span data-stu-id="c6ec4-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="c6ec4-119">`LoggerFactory`ve `AddConsole` için `using` `Microsoft.Extensions.Logging`bir ifade gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="c6ec4-120">Varsayılan ASP.NET Core proje <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>şablonları çağrı , aşağıdaki günlük sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="c6ec4-121">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="c6ec4-122">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="c6ec4-123">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="c6ec4-124">[EventLog](#windows-eventlog-provider) (yalnızca Windows'da çalışırken)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="c6ec4-125">Varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="c6ec4-126">Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="c6ec4-127">Sağlayıcı eklemek için `Add{provider name}` *sağlayıcının*uzatma yöntemini Program.cs olarak arayın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="c6ec4-128">Önceki kod için başvurular `Microsoft.Extensions.Logging` `Microsoft.Extensions.Configuration`ve .</span><span class="sxs-lookup"><span data-stu-id="c6ec4-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="c6ec4-129">Varsayılan proje şablonu çağrıları <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, aşağıdaki günlük sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="c6ec4-130">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-130">Console</span></span>
* <span data-ttu-id="c6ec4-131">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-131">Debug</span></span>
* <span data-ttu-id="c6ec4-132">EventSource (Core 2.2ASP.NETden itibaren)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="c6ec4-133">Kullanırsanız, `CreateDefaultBuilder`varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="c6ec4-134">Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="c6ec4-135">Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="c6ec4-136">Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-136">Create logs</span></span>

<span data-ttu-id="c6ec4-137">Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="c6ec4-138">Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="c6ec4-139">Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .</span><span class="sxs-lookup"><span data-stu-id="c6ec4-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="c6ec4-140">Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="c6ec4-141">Günlük *kategorisi,* her günlükle ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="c6ec4-142">DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="c6ec4-143">Aşağıdaki ana bilgisayar olmayan konsol uygulaması örneği, `LoggingConsoleApp.Program` kategori olarak bir logger oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="c6ec4-144">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="c6ec4-145">Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="c6ec4-146">[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="c6ec4-147">Program sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-147">Create logs in the Program class</span></span>

<span data-ttu-id="c6ec4-148">ASP.NET Core `Program` uygulamasının sınıfına günlük yazmak için, ana bilgisayarı yaptıktan sonra DI'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="c6ec4-149">Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="c6ec4-150">Ancak, ayrı bir logger kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-150">However, a separate logger can be used.</span></span> <span data-ttu-id="c6ec4-151">Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateHostBuilder`açmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="c6ec4-152">`AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="c6ec4-153">Başlangıç sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-153">Create logs in the Startup class</span></span>

<span data-ttu-id="c6ec4-154">ASP.NET Core uygulaması `Startup.Configure` nın yönteminde günlük yazmak `ILogger` için yöntem imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="c6ec4-155">`Startup.ConfigureServices` Yöntemdeki DI kapsayıcı kurulumu tamamlanmadan önce günlükleri yazma desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="c6ec4-156">`Startup` Logger enjeksiyon yapıcı içine desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="c6ec4-157">Yöntem imzasına `Startup.ConfigureServices` logger enjeksiyonu desteklenmez</span><span class="sxs-lookup"><span data-stu-id="c6ec4-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="c6ec4-158">Bu kısıtlamanın nedeni, günlüğe kaydetmenin DI'ye ve yapılandırmaya bağlı olmasıdır, bu da sırayla DI'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="c6ec4-159">DI kapsayıcısı bitene kadar `ConfigureServices` kurulmadı.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="c6ec4-160">Web Barındırıcısı için `Startup` ayrı bir DI kapsayıcı oluşturulduğundan, ASP.NET Core'un önceki sürümlerinde bir logger'ın oluşturucu enjeksiyonu.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="c6ec4-161">Genel Ana Bilgisayar için neden yalnızca bir kapsayıcı oluşturulduğu hakkında bilgi [için, kesme değişikliği duyurusuna](https://github.com/aspnet/Announcements/issues/353)bakın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="c6ec4-162">Bağlı bir hizmeti yapılandırmanız `ILogger<T>`gerekiyorsa, bunu yine de yapıcı enjeksiyon kullanarak veya bir fabrika yöntemi sağlayarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="c6ec4-163">Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="c6ec4-164">Örneğin, bir mülkü DI'den bir hizmetle doldurmanız gerektiğini varsayalım:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="c6ec4-165">Önceki vurgulanan kod, `Func` DI kapsayıcısının bir örneğini oluşturmak için `MyService`ilk kez çalışan bir koddur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="c6ec4-166">Kayıtlı hizmetlerden herhangi biri bu şekilde erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="c6ec4-167">Başlangıç'ta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-167">Create logs in Startup</span></span>

<span data-ttu-id="c6ec4-168">`Startup` Sınıfa günlük yazmak için, `ILogger` oluşturucu imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="c6ec4-169">Program sınıfında günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-169">Create logs in the Program class</span></span>

<span data-ttu-id="c6ec4-170">`Program` Sınıfa günlük yazmak için DI'den bir `ILogger` örnek alın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="c6ec4-171">Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="c6ec4-172">Ancak, ayrı bir logger kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-172">However, a separate logger can be used.</span></span> <span data-ttu-id="c6ec4-173">Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateWebHostBuilder`açmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="c6ec4-174">`AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="c6ec4-175">Asynchronous logger yöntemleri yok</span><span class="sxs-lookup"><span data-stu-id="c6ec4-175">No asynchronous logger methods</span></span>

<span data-ttu-id="c6ec4-176">Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="c6ec4-177">Günlük veri deponuz yavaşsa, doğrudan yazmayın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="c6ec4-178">Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="c6ec4-179">Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="c6ec4-180">Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="c6ec4-181">Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-181">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="c6ec4-182">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-182">Configuration</span></span>

<span data-ttu-id="c6ec4-183">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-183">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="c6ec4-184">Dosya biçimleri (INI, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-184">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="c6ec4-185">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-185">Command-line arguments.</span></span>
* <span data-ttu-id="c6ec4-186">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-186">Environment variables.</span></span>
* <span data-ttu-id="c6ec4-187">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-187">In-memory .NET objects.</span></span>
* <span data-ttu-id="c6ec4-188">Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-188">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="c6ec4-189">[Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-189">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="c6ec4-190">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-190">Custom providers (installed or created).</span></span>

<span data-ttu-id="c6ec4-191">Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-191">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="c6ec4-192">Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-192">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="c6ec4-193">Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-193">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="c6ec4-194">Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-194">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="c6ec4-195">Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-195">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="c6ec4-196">Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-196">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="c6ec4-197">Örnek Konsol sağlayıcısı içindir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-197">The example is for the Console provider.</span></span> <span data-ttu-id="c6ec4-198">Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-198">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="c6ec4-199">Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-199">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="c6ec4-200">`LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-200">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="c6ec4-201">Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-201">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="c6ec4-202">Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-202">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="c6ec4-203">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-203">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="c6ec4-204">Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-204">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="c6ec4-205">Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-205">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="c6ec4-206">Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-206">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="c6ec4-207">Örnek günlük çıktısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-207">Sample logging output</span></span>

<span data-ttu-id="c6ec4-208">Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-208">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="c6ec4-209">Konsol çıkışına bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-209">Here's an example of console output:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

<span data-ttu-id="c6ec4-210">Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-210">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="c6ec4-211">Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-211">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

<span data-ttu-id="c6ec4-212">Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApiSample" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-212">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="c6ec4-213">"Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-213">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="c6ec4-214">ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-214">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="c6ec4-215">Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApi" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-215">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="c6ec4-216">"Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-216">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="c6ec4-217">ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-217">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="c6ec4-218">Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-218">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="c6ec4-219">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="c6ec4-219">NuGet packages</span></span>

<span data-ttu-id="c6ec4-220">Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-220">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="c6ec4-221">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="c6ec4-221">Log category</span></span>

<span data-ttu-id="c6ec4-222">Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-222">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="c6ec4-223">Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-223">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="c6ec4-224">Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-224">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="c6ec4-225">Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-225">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="c6ec4-226">Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-226">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="c6ec4-227">`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-227">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="c6ec4-228">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="c6ec4-228">Log level</span></span>

<span data-ttu-id="c6ec4-229">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-229">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="c6ec4-230">Günlük düzeyi önem veya önemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-230">The log level indicates the severity or importance.</span></span> <span data-ttu-id="c6ec4-231">Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-231">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="c6ec4-232">Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-232">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="c6ec4-233">Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-233">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="c6ec4-234">İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-234">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="c6ec4-235">Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-235">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="c6ec4-236">Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-236">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="c6ec4-237">Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-237">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="c6ec4-238">`Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-238">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="c6ec4-239">Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-239">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="c6ec4-240">ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-240">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="c6ec4-241">İz = 0</span><span class="sxs-lookup"><span data-stu-id="c6ec4-241">Trace = 0</span></span>

  <span data-ttu-id="c6ec4-242">Genellikle yalnızca hata ayıklama için değerli olan bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-242">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="c6ec4-243">Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-243">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="c6ec4-244">*Varsayılan olarak devre dışı bırakılır.*</span><span class="sxs-lookup"><span data-stu-id="c6ec4-244">*Disabled by default.*</span></span>

* <span data-ttu-id="c6ec4-245">Hata Ayıklama = 1</span><span class="sxs-lookup"><span data-stu-id="c6ec4-245">Debug = 1</span></span>

  <span data-ttu-id="c6ec4-246">Geliştirme ve hata ayıklama yararlı olabilir bilgi için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-246">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="c6ec4-247">Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-247">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="c6ec4-248">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="c6ec4-248">Information = 2</span></span>

  <span data-ttu-id="c6ec4-249">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-249">For tracking the general flow of the app.</span></span> <span data-ttu-id="c6ec4-250">Bu günlüklerin genellikle bazı uzun vadeli değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-250">These logs typically have some long-term value.</span></span> <span data-ttu-id="c6ec4-251">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-251">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="c6ec4-252">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="c6ec4-252">Warning = 3</span></span>

  <span data-ttu-id="c6ec4-253">Uygulama akışındaki anormal veya beklenmeyen olaylar için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-253">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="c6ec4-254">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-254">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="c6ec4-255">İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-255">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="c6ec4-256">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-256">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="c6ec4-257">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="c6ec4-257">Error = 4</span></span>

  <span data-ttu-id="c6ec4-258">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-258">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="c6ec4-259">Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-259">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="c6ec4-260">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-260">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="c6ec4-261">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="c6ec4-261">Critical = 5</span></span>

  <span data-ttu-id="c6ec4-262">Acil müdahale gerektiren arızalar için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-262">For failures that require immediate attention.</span></span> <span data-ttu-id="c6ec4-263">Örnekler: disk alanı dışında veri kaybı senaryoları.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-263">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="c6ec4-264">Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-264">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="c6ec4-265">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-265">For example:</span></span>

* <span data-ttu-id="c6ec4-266">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-266">In production:</span></span>
  * <span data-ttu-id="c6ec4-267">`Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-267">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="c6ec4-268">Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-268">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="c6ec4-269">Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-269">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="c6ec4-270">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-270">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="c6ec4-271">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-271">During development:</span></span>
  * <span data-ttu-id="c6ec4-272">İletileri konsola kaydedin. `Warning` `Critical`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-272">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="c6ec4-273">Sorun `Trace` `Information` giderme iletileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-273">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="c6ec4-274">Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-274">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="c6ec4-275">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-275">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="c6ec4-276">Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-276">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="c6ec4-277">Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-277">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="log-event-id"></a><span data-ttu-id="c6ec4-278">Günlük olay kimliği</span><span class="sxs-lookup"><span data-stu-id="c6ec4-278">Log event ID</span></span>

<span data-ttu-id="c6ec4-279">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-279">Each log can specify an *event ID*.</span></span> <span data-ttu-id="c6ec4-280">Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-280">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="c6ec4-281">Olay kimliği, bir dizi olayı ilişkilendirer.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-281">An event ID associates a set of events.</span></span> <span data-ttu-id="c6ec4-282">Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-282">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="c6ec4-283">Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-283">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="c6ec4-284">Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-284">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="c6ec4-285">Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-285">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="c6ec4-286">Günlük ileti şablonu</span><span class="sxs-lookup"><span data-stu-id="c6ec4-286">Log message template</span></span>

<span data-ttu-id="c6ec4-287">Her günlük bir ileti şablonu belirtir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-287">Each log specifies a message template.</span></span> <span data-ttu-id="c6ec4-288">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-288">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="c6ec4-289">Yer tutucular için adları kullanın, sayılar için değil.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-289">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="c6ec4-290">Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-290">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="c6ec4-291">Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-291">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="c6ec4-292">Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-292">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="c6ec4-293">Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-293">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="c6ec4-294">Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-294">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="c6ec4-295">Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-295">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="c6ec4-296">Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-296">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="c6ec4-297">Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-297">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="c6ec4-298">Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-298">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="c6ec4-299">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="c6ec4-299">Logging exceptions</span></span>

<span data-ttu-id="c6ec4-300">Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-300">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="c6ec4-301">Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-301">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="c6ec4-302">Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-302">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="c6ec4-303">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="c6ec4-303">Log filtering</span></span>

<span data-ttu-id="c6ec4-304">Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-304">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="c6ec4-305">Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-305">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="c6ec4-306">Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-306">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="c6ec4-307">İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-307">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="c6ec4-308">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="c6ec4-308">Create filter rules in configuration</span></span>

<span data-ttu-id="c6ec4-309">Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-309">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="c6ec4-310">Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-310">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="c6ec4-311">Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-311">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="c6ec4-312">Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-312">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="c6ec4-313">Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-313">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="c6ec4-314">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="c6ec4-314">Filter rules in code</span></span>

<span data-ttu-id="c6ec4-315">Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-315">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="c6ec4-316">İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-316">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="c6ec4-317">İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-317">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="c6ec4-318">Filtreleme kuralları nasıl uygulanır?</span><span class="sxs-lookup"><span data-stu-id="c6ec4-318">How filtering rules are applied</span></span>

<span data-ttu-id="c6ec4-319">Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-319">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="c6ec4-320">İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-320">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="c6ec4-321">Sayı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-321">Number</span></span> | <span data-ttu-id="c6ec4-322">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-322">Provider</span></span>      | <span data-ttu-id="c6ec4-323">Ile başlayan kategoriler ...</span><span class="sxs-lookup"><span data-stu-id="c6ec4-323">Categories that begin with ...</span></span>          | <span data-ttu-id="c6ec4-324">Minimum günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="c6ec4-324">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="c6ec4-325">1</span><span class="sxs-lookup"><span data-stu-id="c6ec4-325">1</span></span>      | <span data-ttu-id="c6ec4-326">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-326">Debug</span></span>         | <span data-ttu-id="c6ec4-327">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="c6ec4-327">All categories</span></span>                          | <span data-ttu-id="c6ec4-328">Bilgi</span><span class="sxs-lookup"><span data-stu-id="c6ec4-328">Information</span></span>       |
| <span data-ttu-id="c6ec4-329">2</span><span class="sxs-lookup"><span data-stu-id="c6ec4-329">2</span></span>      | <span data-ttu-id="c6ec4-330">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-330">Console</span></span>       | <span data-ttu-id="c6ec4-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="c6ec4-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="c6ec4-332">Uyarı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-332">Warning</span></span>           |
| <span data-ttu-id="c6ec4-333">3</span><span class="sxs-lookup"><span data-stu-id="c6ec4-333">3</span></span>      | <span data-ttu-id="c6ec4-334">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-334">Console</span></span>       | <span data-ttu-id="c6ec4-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="c6ec4-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="c6ec4-336">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-336">Debug</span></span>             |
| <span data-ttu-id="c6ec4-337">4</span><span class="sxs-lookup"><span data-stu-id="c6ec4-337">4</span></span>      | <span data-ttu-id="c6ec4-338">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-338">Console</span></span>       | <span data-ttu-id="c6ec4-339">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="c6ec4-339">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="c6ec4-340">Hata</span><span class="sxs-lookup"><span data-stu-id="c6ec4-340">Error</span></span>             |
| <span data-ttu-id="c6ec4-341">5</span><span class="sxs-lookup"><span data-stu-id="c6ec4-341">5</span></span>      | <span data-ttu-id="c6ec4-342">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-342">Console</span></span>       | <span data-ttu-id="c6ec4-343">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="c6ec4-343">All categories</span></span>                          | <span data-ttu-id="c6ec4-344">Bilgi</span><span class="sxs-lookup"><span data-stu-id="c6ec4-344">Information</span></span>       |
| <span data-ttu-id="c6ec4-345">6</span><span class="sxs-lookup"><span data-stu-id="c6ec4-345">6</span></span>      | <span data-ttu-id="c6ec4-346">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="c6ec4-346">All providers</span></span> | <span data-ttu-id="c6ec4-347">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="c6ec4-347">All categories</span></span>                          | <span data-ttu-id="c6ec4-348">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-348">Debug</span></span>             |
| <span data-ttu-id="c6ec4-349">7</span><span class="sxs-lookup"><span data-stu-id="c6ec4-349">7</span></span>      | <span data-ttu-id="c6ec4-350">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="c6ec4-350">All providers</span></span> | <span data-ttu-id="c6ec4-351">Sistem</span><span class="sxs-lookup"><span data-stu-id="c6ec4-351">System</span></span>                                  | <span data-ttu-id="c6ec4-352">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-352">Debug</span></span>             |
| <span data-ttu-id="c6ec4-353">8</span><span class="sxs-lookup"><span data-stu-id="c6ec4-353">8</span></span>      | <span data-ttu-id="c6ec4-354">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-354">Debug</span></span>         | <span data-ttu-id="c6ec4-355">Microsoft</span><span class="sxs-lookup"><span data-stu-id="c6ec4-355">Microsoft</span></span>                               | <span data-ttu-id="c6ec4-356">İzleme</span><span class="sxs-lookup"><span data-stu-id="c6ec4-356">Trace</span></span>             |

<span data-ttu-id="c6ec4-357">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-357">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="c6ec4-358">Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-358">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="c6ec4-359">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-359">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="c6ec4-360">Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-360">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="c6ec4-361">Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-361">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="c6ec4-362">Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-362">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="c6ec4-363">Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-363">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="c6ec4-364">Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-364">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="c6ec4-365">Birden çok kural seçilirse, **sonuncusunu** alın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-365">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="c6ec4-366">Kural seçili değilse, `MinimumLevel`kullanın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-366">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="c6ec4-367">Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-367">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="c6ec4-368">Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-368">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="c6ec4-369">Kural 8 çok özeldir, bu yüzden seçilen kuraldır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-369">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="c6ec4-370">Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-370">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="c6ec4-371">Kural 3 çok özeldir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-371">Rule 3 is most specific.</span></span>

<span data-ttu-id="c6ec4-372">Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-372">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="c6ec4-373">`Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-373">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="c6ec4-374">Sağlayıcı diğer adlar</span><span class="sxs-lookup"><span data-stu-id="c6ec4-374">Provider aliases</span></span>

<span data-ttu-id="c6ec4-375">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-375">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="c6ec4-376">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-376">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="c6ec4-377">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-377">Console</span></span>
* <span data-ttu-id="c6ec4-378">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-378">Debug</span></span>
* <span data-ttu-id="c6ec4-379">EventSource</span><span class="sxs-lookup"><span data-stu-id="c6ec4-379">EventSource</span></span>
* <span data-ttu-id="c6ec4-380">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c6ec4-380">EventLog</span></span>
* <span data-ttu-id="c6ec4-381">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6ec4-381">TraceSource</span></span>
* <span data-ttu-id="c6ec4-382">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c6ec4-382">AzureAppServicesFile</span></span>
* <span data-ttu-id="c6ec4-383">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c6ec4-383">AzureAppServicesBlob</span></span>
* <span data-ttu-id="c6ec4-384">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c6ec4-384">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="c6ec4-385">Varsayılan minimum düzey</span><span class="sxs-lookup"><span data-stu-id="c6ec4-385">Default minimum level</span></span>

<span data-ttu-id="c6ec4-386">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-386">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="c6ec4-387">Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-387">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="c6ec4-388">Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-388">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="c6ec4-389">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="c6ec4-389">Filter functions</span></span>

<span data-ttu-id="c6ec4-390">Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-390">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="c6ec4-391">İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-391">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="c6ec4-392">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-392">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="c6ec4-393">Sistem kategorileri ve düzeyleri</span><span class="sxs-lookup"><span data-stu-id="c6ec4-393">System categories and levels</span></span>

<span data-ttu-id="c6ec4-394">ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-394">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="c6ec4-395">Kategori</span><span class="sxs-lookup"><span data-stu-id="c6ec4-395">Category</span></span>                            | <span data-ttu-id="c6ec4-396">Notlar</span><span class="sxs-lookup"><span data-stu-id="c6ec4-396">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="c6ec4-397">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="c6ec4-397">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="c6ec4-398">General ASP.NET Core teşhisi.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-398">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="c6ec4-399">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="c6ec4-399">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="c6ec4-400">Hangi anahtarlar düşünüldü, bulundu ve kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-400">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="c6ec4-401">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="c6ec4-401">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="c6ec4-402">Ev sahiplerine izin verildi.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-402">Hosts allowed.</span></span> |
| <span data-ttu-id="c6ec4-403">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="c6ec4-403">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="c6ec4-404">HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-404">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="c6ec4-405">Hangi barındırma başlangıç meclisleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-405">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="c6ec4-406">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="c6ec4-406">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="c6ec4-407">MVC ve Razor teşhis.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-407">MVC and Razor diagnostics.</span></span> <span data-ttu-id="c6ec4-408">Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-408">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="c6ec4-409">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="c6ec4-409">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="c6ec4-410">Rota eşleştirme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-410">Route matching information.</span></span> |
| <span data-ttu-id="c6ec4-411">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="c6ec4-411">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="c6ec4-412">Bağlantı başlatın, durdurun ve yanıtları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-412">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="c6ec4-413">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-413">HTTPS certificate information.</span></span> |
| <span data-ttu-id="c6ec4-414">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="c6ec4-414">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="c6ec4-415">Dosyalar servis edilebis.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-415">Files served.</span></span> |
| <span data-ttu-id="c6ec4-416">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c6ec4-416">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="c6ec4-417">Genel Varlık Çerçeve Çekirdek tanılama.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-417">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="c6ec4-418">Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-418">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="c6ec4-419">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="c6ec4-419">Log scopes</span></span>

 <span data-ttu-id="c6ec4-420">*Kapsam,* bir dizi mantıksal işlemi gruplayabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-420">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="c6ec4-421">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-421">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="c6ec4-422">Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-422">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="c6ec4-423">Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-423">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="c6ec4-424">Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-424">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="c6ec4-425">Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-425">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="c6ec4-426">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-426">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="c6ec4-427">Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-427">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="c6ec4-428">Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-428">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="c6ec4-429">Her günlük iletisi kapsamlı bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-429">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="c6ec4-430">Yerleşik günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="c6ec4-430">Built-in logging providers</span></span>

<span data-ttu-id="c6ec4-431">ASP.NET Core aşağıdaki sağlayıcıları gemiler:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-431">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="c6ec4-432">Konsol</span><span class="sxs-lookup"><span data-stu-id="c6ec4-432">Console</span></span>](#console-provider)
* [<span data-ttu-id="c6ec4-433">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-433">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="c6ec4-434">Olaykaynağı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-434">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="c6ec4-435">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c6ec4-435">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="c6ec4-436">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6ec4-436">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="c6ec4-437">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c6ec4-437">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c6ec4-438">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c6ec4-438">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c6ec4-439">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c6ec4-439">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="c6ec4-440">ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis></span><span class="sxs-lookup"><span data-stu-id="c6ec4-440">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="c6ec4-441">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-441">Console provider</span></span>

<span data-ttu-id="c6ec4-442">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-442">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="c6ec4-443">Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-443">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="c6ec4-444">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-444">Debug provider</span></span>

<span data-ttu-id="c6ec4-445">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-445">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="c6ec4-446">Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-446">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="c6ec4-447">Olay Kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-447">Event Source provider</span></span>

<span data-ttu-id="c6ec4-448">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-448">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="c6ec4-449">Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-449">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="c6ec4-450">Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-450">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="c6ec4-451">dotnet izleme takım</span><span class="sxs-lookup"><span data-stu-id="c6ec4-451">dotnet trace tooling</span></span>

<span data-ttu-id="c6ec4-452">[Dotnet izleme](/dotnet/core/diagnostics/dotnet-trace) aracı, çalışan bir işlemin .NET Core izlerinin toplanmasını sağlayan bir çapraz platform CLI global aracıdır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-452">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="c6ec4-453">Araç <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verilerini bir <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-453">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="c6ec4-454">Aşağıdaki komutla dotnet izleme aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-454">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="c6ec4-455">Bir uygulamadan iz toplamak için dotnet izleme aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-455">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="c6ec4-456">Uygulama ana bilgisayarı `CreateDefaultBuilder`oluşturmuyorsa, Olay Kaynağı [sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-456">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="c6ec4-457">Uygulamayı komutla `dotnet run` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-457">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="c6ec4-458">.NET Core uygulamasının işlem tanımlayıcısını (PID) belirleyin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-458">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="c6ec4-459">Windows'da aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-459">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="c6ec4-460">Görev Yöneticisi (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-460">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="c6ec4-461">görev listesi komutu</span><span class="sxs-lookup"><span data-stu-id="c6ec4-461">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="c6ec4-462">Get-Process Powershell komutu</span><span class="sxs-lookup"><span data-stu-id="c6ec4-462">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="c6ec4-463">Linux'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-463">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="c6ec4-464">Uygulamanın derlemesi ile aynı ada sahip işlem için PID'yi bulun.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-464">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="c6ec4-465">Komutu `dotnet trace` uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-465">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="c6ec4-466">Genel komut sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-466">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="c6ec4-467">PowerShell komut uyruğunu kullanırken, değeri tek tırnak içine alabilen `--providers` (`'`):</span><span class="sxs-lookup"><span data-stu-id="c6ec4-467">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="c6ec4-468">Windows olmayan platformlarda, `-f speedscope` çıktı izleme dosyasının biçimini `speedscope`''ye değiştirme seçeneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-468">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="c6ec4-469">Anahtar kelime</span><span class="sxs-lookup"><span data-stu-id="c6ec4-469">Keyword</span></span> | <span data-ttu-id="c6ec4-470">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-470">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="c6ec4-471">1</span><span class="sxs-lookup"><span data-stu-id="c6ec4-471">1</span></span>       | <span data-ttu-id="c6ec4-472">Hakkında meta olayları `LoggingEventSource`günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-472">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="c6ec4-473">Olayları kaydetmez). `ILogger`</span><span class="sxs-lookup"><span data-stu-id="c6ec4-473">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="c6ec4-474">2</span><span class="sxs-lookup"><span data-stu-id="c6ec4-474">2</span></span>       | <span data-ttu-id="c6ec4-475">Çağrıldığında `Message` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-475">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="c6ec4-476">Bilgileri programatik (biçimlendirilmemiş) bir şekilde sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-476">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="c6ec4-477">4</span><span class="sxs-lookup"><span data-stu-id="c6ec4-477">4</span></span>       | <span data-ttu-id="c6ec4-478">Çağrıldığında `FormatMessage` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-478">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="c6ec4-479">Bilgilerin biçimlendirilmiş dize sürümünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-479">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="c6ec4-480">8</span><span class="sxs-lookup"><span data-stu-id="c6ec4-480">8</span></span>       | <span data-ttu-id="c6ec4-481">Çağrıldığında `MessageJson` `ILogger.Log()` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-481">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="c6ec4-482">Bağımsız değişkenlerin JSON temsilini sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-482">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="c6ec4-483">Etkinlik Düzeyi</span><span class="sxs-lookup"><span data-stu-id="c6ec4-483">Event Level</span></span> | <span data-ttu-id="c6ec4-484">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c6ec4-484">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="c6ec4-485">0</span><span class="sxs-lookup"><span data-stu-id="c6ec4-485">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="c6ec4-486">1</span><span class="sxs-lookup"><span data-stu-id="c6ec4-486">1</span></span>           | `Critical`      |
   | <span data-ttu-id="c6ec4-487">2</span><span class="sxs-lookup"><span data-stu-id="c6ec4-487">2</span></span>           | `Error`         |
   | <span data-ttu-id="c6ec4-488">3</span><span class="sxs-lookup"><span data-stu-id="c6ec4-488">3</span></span>           | `Warning`       |
   | <span data-ttu-id="c6ec4-489">4</span><span class="sxs-lookup"><span data-stu-id="c6ec4-489">4</span></span>           | `Informational` |
   | <span data-ttu-id="c6ec4-490">5</span><span class="sxs-lookup"><span data-stu-id="c6ec4-490">5</span></span>           | `Verbose`       |

   <span data-ttu-id="c6ec4-491">`FilterSpecs`için `{Logger Category}` girişler ve `{Event Level}` ek günlük filtreleme koşulları temsil.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-491">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="c6ec4-492">Bir `FilterSpecs` semicolon ile`;`ayrı girişleri ( ).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-492">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="c6ec4-493">Windows komut uyruğunu kullanarak örnek `--providers` (değer etrafında tek bir tırnak işareti**yok):**</span><span class="sxs-lookup"><span data-stu-id="c6ec4-493">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="c6ec4-494">Önceki komut etkinleştirir:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-494">The preceding command activates:</span></span>

   * <span data-ttu-id="c6ec4-495">Hatalar için biçimlendirilmiş dizeleri (`4`) üretmek`2`için Olay Kaynağı kaydedici ( ).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-495">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="c6ec4-496">`Microsoft.AspNetCore.Hosting``Informational` günlük düzeyinde günlüğe`4`kaydetme ( ).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-496">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="c6ec4-497">Enter tuşuna veya Ctrl+C tuşuna basarak dotnet izleme aletini durdurun.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-497">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="c6ec4-498">İzleme, `dotnet trace` komutun yürütüldüğü klasöre *trace.nettrace* adı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-498">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="c6ec4-499">[Perfview](#perfview)ile iz açın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-499">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="c6ec4-500">*trace.nettrace* dosyasını açın ve izleme olaylarını keşfedin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-500">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="c6ec4-501">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-501">For more information, see:</span></span>

* <span data-ttu-id="c6ec4-502">[Performans analizi yardımcı programı (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core dokümantasyon) için izleme</span><span class="sxs-lookup"><span data-stu-id="c6ec4-502">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="c6ec4-503">[Performans analizi yardımcı programı (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub depo belgeleri) için izleme</span><span class="sxs-lookup"><span data-stu-id="c6ec4-503">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="c6ec4-504">[LoggingEventSource Sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Tarayıcısı)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-504">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="c6ec4-505">[LoggingEventSource referans kaynağı (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Farklı bir sürüm için referans `release/{Version}`kaynağı `{Version}` elde etmek için, şubeyi , ASP.NET Core sürümü istenilen nerede değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-505">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="c6ec4-506">[Perfview](#perfview) &ndash; Olay Kaynağı izlerini görüntülemek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-506">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="c6ec4-507">Perfview</span><span class="sxs-lookup"><span data-stu-id="c6ec4-507">Perfview</span></span>

::: moniker-end

<span data-ttu-id="c6ec4-508">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-508">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="c6ec4-509">ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-509">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="c6ec4-510">PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-510">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="c6ec4-511">(Dize başında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-511">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="c6ec4-513">Windows EventLog sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-513">Windows EventLog provider</span></span>

<span data-ttu-id="c6ec4-514">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-514">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="c6ec4-515">[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-515">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="c6ec4-516">`null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-516">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="c6ec4-517">`LogName`&ndash; "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="c6ec4-517">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="c6ec4-518">`SourceName`&ndash; ".NET Çalışma Zamanı"</span><span class="sxs-lookup"><span data-stu-id="c6ec4-518">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="c6ec4-519">`MachineName`&ndash; yerel makine</span><span class="sxs-lookup"><span data-stu-id="c6ec4-519">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="c6ec4-520">Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-520">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="c6ec4-521">Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-521">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="c6ec4-522">Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-522">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="c6ec4-523">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-523">TraceSource provider</span></span>

<span data-ttu-id="c6ec4-524">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-524">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="c6ec4-525">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-525">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="c6ec4-526">Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-526">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="c6ec4-527">Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-527">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="c6ec4-528">Azure Uygulama Hizmeti sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-528">Azure App Service provider</span></span>

<span data-ttu-id="c6ec4-529">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-529">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6ec4-530">Sağlayıcı paketi paylaşılan çerçeveye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-530">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="c6ec4-531">Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-531">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6ec4-532">Sağlayıcı paketi [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-532">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c6ec4-533">.NET Framework'u hedef alırken `Microsoft.AspNetCore.App` veya meta pakete başvururken, sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-533">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6ec4-534">Sağlayıcı ayarlarını yapılandırmak <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>için aşağıdaki örnekte gösterildiği gibi kullanın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-534">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c6ec4-535">Sağlayıcı ayarlarını yapılandırmak <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>için aşağıdaki örnekte gösterildiği gibi kullanın ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-535">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c6ec4-536">Aşırı <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> yükleme içeri geçmeni <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>sağlar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-536">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="c6ec4-537">Ayarlar nesnesi, günlüğe kaydetme çıktısı şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-537">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="c6ec4-538">(*Çıktı şablonu,* bir yöntem çağrısıyla sağlanana ek olarak tüm `ILogger` günlüklere uygulanan bir ileti şablonudur.)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-538">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="c6ec4-539">Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-539">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="c6ec4-540">Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-540">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="c6ec4-541">**Uygulama Günlüğü (Filesystem)**</span><span class="sxs-lookup"><span data-stu-id="c6ec4-541">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="c6ec4-542">**Uygulama Günlüğü (Blob)**</span><span class="sxs-lookup"><span data-stu-id="c6ec4-542">**Application Logging (Blob)**</span></span>

<span data-ttu-id="c6ec4-543">Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-543">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="c6ec4-544">Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-544">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="c6ec4-545">Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-545">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="c6ec4-546">Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-546">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="c6ec4-547">Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-547">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="c6ec4-548">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="c6ec4-548">Azure log streaming</span></span>

<span data-ttu-id="c6ec4-549">Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-549">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="c6ec4-550">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="c6ec4-550">The app server</span></span>
* <span data-ttu-id="c6ec4-551">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="c6ec4-551">The web server</span></span>
* <span data-ttu-id="c6ec4-552">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="c6ec4-552">Failed request tracing</span></span>

<span data-ttu-id="c6ec4-553">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-553">To configure Azure log streaming:</span></span>

* <span data-ttu-id="c6ec4-554">Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-554">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="c6ec4-555">**Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**</span><span class="sxs-lookup"><span data-stu-id="c6ec4-555">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="c6ec4-556">Günlük **Düzeyi'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-556">Choose the log **Level**.</span></span> <span data-ttu-id="c6ec4-557">Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-557">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="c6ec4-558">Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-558">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="c6ec4-559">Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-559">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="c6ec4-560">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="c6ec4-560">Azure Application Insights trace logging</span></span>

<span data-ttu-id="c6ec4-561">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-561">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="c6ec4-562">Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-562">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="c6ec4-563">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-563">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="c6ec4-564">Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-564">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="c6ec4-565">Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-565">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="c6ec4-566">ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-566">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="c6ec4-567">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-567">For more information, see the following resources:</span></span>

* [<span data-ttu-id="c6ec4-568">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="c6ec4-568">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="c6ec4-569">[ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-569">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="c6ec4-570">[.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-570">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="c6ec4-571">[Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="c6ec4-571">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="c6ec4-572">Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)</span><span class="sxs-lookup"><span data-stu-id="c6ec4-572">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="c6ec4-573">Üçüncü taraf günlük sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="c6ec4-573">Third-party logging providers</span></span>

<span data-ttu-id="c6ec4-574">ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-574">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="c6ec4-575">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-575">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="c6ec4-576">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-576">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="c6ec4-577">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-577">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="c6ec4-578">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-578">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="c6ec4-579">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-579">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="c6ec4-580">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-580">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="c6ec4-581">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-581">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="c6ec4-582">[Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-582">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="c6ec4-583">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-583">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="c6ec4-584">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="c6ec4-584">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="c6ec4-585">Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-585">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="c6ec4-586">Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="c6ec4-586">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="c6ec4-587">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-587">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="c6ec4-588">Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-588">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="c6ec4-589">Daha fazla bilgi için her sağlayıcının belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-589">For more information, see each provider's documentation.</span></span> <span data-ttu-id="c6ec4-590">Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c6ec4-590">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6ec4-591">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c6ec4-591">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
