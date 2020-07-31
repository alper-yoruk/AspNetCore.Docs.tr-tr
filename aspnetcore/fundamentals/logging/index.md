---
title: .NET Core ve ASP.NET Core'da günlük
author: rick-anderson
description: Microsoft. Extensions. Logging NuGet paketi tarafından sunulan günlüğe kaydetme çerçevesini nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330769"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="d2524-103">.NET Core ve ASP.NET Core'da günlük</span><span class="sxs-lookup"><span data-stu-id="d2524-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d2524-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d2524-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d2524-105">.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="d2524-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d2524-106">Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d2524-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="d2524-107">Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalardan oluşur.</span><span class="sxs-lookup"><span data-stu-id="d2524-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="d2524-108">Bu kod parçacıklarının günlüğe kaydetmeye özgü bölümleri, [genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanan tüm .NET Core uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="d2524-109">ASP.NET Core Web uygulaması şablonları genel Konağı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="d2524-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2524-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="d2524-111">Günlüğe kaydetme sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-111">Logging providers</span></span>

<span data-ttu-id="d2524-112">Günlükleri görüntüleyen sağlayıcı dışında günlüğe kaydetme sağlayıcıları mağaza günlükleri `Console` .</span><span class="sxs-lookup"><span data-stu-id="d2524-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="d2524-113">Örneğin, Azure Application Insights sağlayıcısı günlükleri Azure Application Insights depolar.</span><span class="sxs-lookup"><span data-stu-id="d2524-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="d2524-114">Birden çok sağlayıcı etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="d2524-115">Varsayılan ASP.NET Core Web uygulaması şablonları:</span><span class="sxs-lookup"><span data-stu-id="d2524-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="d2524-116">[Genel Konağı](xref:fundamentals/host/generic-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="d2524-117"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Aşağıdaki günlük sağlayıcılarını ekleyen çağırın:</span><span class="sxs-lookup"><span data-stu-id="d2524-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="d2524-118">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-118">Console</span></span>](#console)
  * [<span data-ttu-id="d2524-119">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="d2524-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="d2524-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="d2524-121">[EventLog](#welog): yalnızca Windows</span><span class="sxs-lookup"><span data-stu-id="d2524-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="d2524-122">Yukarıdaki kod, `Program` ASP.NET Core Web uygulaması şablonlarıyla oluşturulan sınıfı gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="d2524-123">Sonraki birkaç bölüm, genel ana bilgisayarı kullanan ASP.NET Core Web uygulaması şablonlarına göre örnekler sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="d2524-124">[Konak olmayan konsol uygulamaları](#nhca) bu belgenin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="d2524-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="d2524-125">Tarafından eklenen varsayılan günlük sağlayıcıları kümesini geçersiz kılmak için `Host.CreateDefaultBuilder` `ClearProviders` gerekli günlük sağlayıcılarını çağırın ve ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="d2524-126">Örneğin, aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d2524-126">For example, the following code:</span></span>

* <span data-ttu-id="d2524-127"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>Oluşturucunun tüm örneklerini kaldırmak için çağırır <xref:Microsoft.Extensions.Logging.ILoggerProvider> .</span><span class="sxs-lookup"><span data-stu-id="d2524-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="d2524-128">[Konsol](#console) günlüğü sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="d2524-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="d2524-129">Ek sağlayıcılar için bkz.:</span><span class="sxs-lookup"><span data-stu-id="d2524-129">For additional providers, see:</span></span>

* [<span data-ttu-id="d2524-130">Yerleşik günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="d2524-131">[Üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="d2524-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="d2524-132">Günlükleri oluştur</span><span class="sxs-lookup"><span data-stu-id="d2524-132">Create logs</span></span>

<span data-ttu-id="d2524-133">Günlükler oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) öğesinden bir nesne kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d2524-134">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="d2524-134">The following example:</span></span>

* <span data-ttu-id="d2524-135">`ILogger<AboutModel>`Türün tam adı olan bir günlük *kategorisini* kullanan bir günlükçü oluşturur `AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="d2524-136">Günlük kategorisi, her günlük ile ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="d2524-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="d2524-137">Bu <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> düzeyde günlüğe kaydedilecek çağrılar `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="d2524-138">Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="d2524-139">[Düzeyler](#log-level) ve [Kategoriler](#log-category) bu belgenin ilerleyen bölümlerinde daha ayrıntılı açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d2524-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="d2524-140">Hakkında bilgi için Blazor , bkz. bu belgede [Günlükler Blazor Blazor WebAssembly oluşturma](#clib) .</span><span class="sxs-lookup"><span data-stu-id="d2524-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="d2524-141">[Ana ve başlangıçta Günlükler oluşturma](#clms) , ve ' de günlüklerin nasıl oluşturulacağını `Main` gösterir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d2524-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="d2524-142">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d2524-142">Configure logging</span></span>

<span data-ttu-id="d2524-143">Günlüğe kaydetme yapılandırması genellikle `Logging` *appSettings*'in bölümü tarafından sağlanır. `{Environment}` *. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d2524-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="d2524-144">Dosyasında aşağıdaki *appsettings.Development.js* ASP.NET Core Web uygulaması şablonları tarafından oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d2524-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="d2524-145">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="d2524-145">In the preceding JSON:</span></span>

* <span data-ttu-id="d2524-146">`"Default"`, `"Microsoft"` Ve `"Microsoft.Hosting.Lifetime"` kategorileri belirtilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="d2524-147">`"Microsoft"`Kategori, ile başlayan tüm kategoriler için geçerlidir `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="d2524-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="d2524-148">Örneğin, bu ayar kategori için geçerlidir `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` .</span><span class="sxs-lookup"><span data-stu-id="d2524-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="d2524-149">`"Microsoft"`Günlük düzeyindeki `Warning` ve daha yüksek kategori günlükleri.</span><span class="sxs-lookup"><span data-stu-id="d2524-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="d2524-150">Kategori kategorisinden `"Microsoft.Hosting.Lifetime"` daha özeldir `"Microsoft"` , bu nedenle `"Microsoft.Hosting.Lifetime"` Kategori "Information" ve üzeri günlük düzeyinde günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="d2524-151">Belirli bir günlük sağlayıcısı belirtilmedi, bu nedenle `LogLevel` [Windows olay](#welog)günlüğü hariç tüm etkin günlük sağlayıcıları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="d2524-152">`Logging`Özelliği <xref:Microsoft.Extensions.Logging.LogLevel> ve günlük sağlayıcısı özelliklerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="d2524-153">`LogLevel`Seçili kategoriler için günlüğe kaydedilecek en düşük [düzeyi](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="d2524-154">Önceki JSON 'da `Information` ve `Warning` günlük düzeyleri belirtilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="d2524-155">`LogLevel`Günlüğün önem derecesini ve 0 ile 6 arasındaki aralıkları belirtir:</span><span class="sxs-lookup"><span data-stu-id="d2524-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="d2524-156">`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 ve `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="d2524-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="d2524-157">Bir `LogLevel` belirtildiğinde, belirtilen düzeydeki ve daha yüksek iletiler için günlüğe kaydetme etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="d2524-158">Önceki JSON 'da `Default` Kategori, ve üzeri için günlüğe kaydedilir `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="d2524-159">Örneğin,, `Information` , `Warning` `Error` ve `Critical` iletileri günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="d2524-160">Hayır `LogLevel` belirtilmemişse, günlüğe kaydetme varsayılan olarak `Information` düzeydir.</span><span class="sxs-lookup"><span data-stu-id="d2524-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="d2524-161">Daha fazla bilgi için bkz. [günlük düzeyleri](#llvl).</span><span class="sxs-lookup"><span data-stu-id="d2524-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="d2524-162">Sağlayıcı özelliği, bir özelliği belirtebilir `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="d2524-163">`LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir ve sağlayıcı olmayan günlük ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d2524-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="d2524-164">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d2524-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="d2524-165">`Logging.{providername}.LogLevel`' Deki geçersiz kılma ayarlarındaki ayarlar `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="d2524-166">Önceki JSON 'da, `Debug` sağlayıcının varsayılan günlük düzeyi şu şekilde ayarlanır `Information` :</span><span class="sxs-lookup"><span data-stu-id="d2524-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="d2524-167">Yukarıdaki ayar, `Information` hariç her kategori için günlük düzeyini belirtir `Logging:Debug:` `Microsoft.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="d2524-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="d2524-168">Belirli bir kategori listelendiğinde, belirtilen kategori varsayılan kategoriyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d2524-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="d2524-169">Önceki JSON 'da `Logging:Debug:LogLevel` Kategoriler `"Microsoft.Hosting"` ve `"Default"` içindeki ayarları geçersiz kılın`Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="d2524-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="d2524-170">En düşük günlük düzeyi şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="d2524-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="d2524-171">Belirli sağlayıcılar: Örneğin,`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="d2524-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="d2524-172">Belirli Kategoriler: Örneğin,`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="d2524-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="d2524-173">Tüm sağlayıcılar ve tüm Kategoriler:`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="d2524-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="d2524-174">Minimum düzeyin altındaki tüm Günlükler şu ***değildir***:</span><span class="sxs-lookup"><span data-stu-id="d2524-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="d2524-175">Sağlayıcıya geçirildi.</span><span class="sxs-lookup"><span data-stu-id="d2524-175">Passed to the provider.</span></span>
* <span data-ttu-id="d2524-176">Günlüğe kaydedilir veya gösterilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-176">Logged or displayed.</span></span>

<span data-ttu-id="d2524-177">Tüm günlükleri gizlemek için [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel)belirtin.</span><span class="sxs-lookup"><span data-stu-id="d2524-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="d2524-178">`LogLevel.None`6 değerine sahiptir `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="d2524-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="d2524-179">Bir sağlayıcı, [günlük kapsamlarını](#logscopes)destekliyorsa, etkinleştirilip etkinleştirilmeyeceğini `IncludeScopes` belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="d2524-180">Daha fazla bilgi için bkz. [günlük kapsamları](#logscopes)</span><span class="sxs-lookup"><span data-stu-id="d2524-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="d2524-181">Aşağıdaki *appsettings.js* dosyası varsayılan olarak etkinleştirilen tüm sağlayıcıları içerir:</span><span class="sxs-lookup"><span data-stu-id="d2524-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="d2524-182">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="d2524-182">In the preceding sample:</span></span>

* <span data-ttu-id="d2524-183">Kategoriler ve düzeyler önerilen değerler değildir.</span><span class="sxs-lookup"><span data-stu-id="d2524-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="d2524-184">Varsayılan tüm sağlayıcıları göstermek için örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="d2524-185">`Logging.{providername}.LogLevel`' Deki geçersiz kılma ayarlarındaki ayarlar `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="d2524-186">Örneğin, içindeki düzeyi `Debug.LogLevel.Default` içindeki düzeyi geçersiz kılar `LogLevel.Default` .</span><span class="sxs-lookup"><span data-stu-id="d2524-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="d2524-187">Her varsayılan sağlayıcı *diğer adı* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d2524-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="d2524-188">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="d2524-189">Yerleşik sağlayıcılar diğer adları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d2524-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="d2524-190">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-190">Console</span></span>
  * <span data-ttu-id="d2524-191">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-191">Debug</span></span>
  * <span data-ttu-id="d2524-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="d2524-192">EventSource</span></span>
  * <span data-ttu-id="d2524-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="d2524-193">EventLog</span></span>
  * <span data-ttu-id="d2524-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d2524-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="d2524-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d2524-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="d2524-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d2524-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="d2524-197">Günlük düzeyini komut satırı, ortam değişkenleri ve diğer yapılandırma ile ayarlama</span><span class="sxs-lookup"><span data-stu-id="d2524-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="d2524-198">Günlük düzeyi herhangi bir [yapılandırma sağlayıcısından](xref:fundamentals/configuration/index)ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d2524-199">Aşağıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="d2524-199">The following commands:</span></span>

* <span data-ttu-id="d2524-200">Ortam anahtarını `Logging:LogLevel:Microsoft` Windows üzerinde bir değeri olarak ayarlayın `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="d2524-201">ASP.NET Core Web uygulaması şablonlarıyla oluşturulan bir uygulama kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="d2524-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="d2524-202">`dotnet run`Komutun kullanıldıktan sonra proje dizininde çalıştırılması gerekir `set` .</span><span class="sxs-lookup"><span data-stu-id="d2524-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="d2524-203">Önceki ortam ayarı:</span><span class="sxs-lookup"><span data-stu-id="d2524-203">The preceding environment setting:</span></span>

* <span data-ttu-id="d2524-204">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d2524-205">Visual Studio ile başlatılan tarayıcılarda okunamaz.</span><span class="sxs-lookup"><span data-stu-id="d2524-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d2524-206">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutu, Windows 'daki ortam anahtarını ve değerini de ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="d2524-207">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d2524-208">`/M`Anahtar, değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="d2524-209">`/M`Kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="d2524-210">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d2524-211">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d2524-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d2524-212">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d2524-213">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="d2524-213">Exposed as environment variables.</span></span>

<span data-ttu-id="d2524-214">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d2524-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d2524-215">Ortam değişkenlerini kullanarak ASP.NET Core yapılandırma değerlerini ayarlama hakkında daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="d2524-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="d2524-216">Komut satırı, Azure Key Vault, Azure Uygulama yapılandırması, diğer dosya biçimleri ve daha fazlası dahil diğer yapılandırma kaynaklarını kullanma hakkında bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="d2524-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="d2524-217">Filtreleme kuralları nasıl uygulanır</span><span class="sxs-lookup"><span data-stu-id="d2524-217">How filtering rules are applied</span></span>

<span data-ttu-id="d2524-218">Bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesne oluşturulduğunda, <xref:Microsoft.Extensions.Logging.ILoggerFactory> nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="d2524-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="d2524-219">Bir örnek tarafından yazılan tüm iletiler `ILogger` , seçilen kurallara göre filtrelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="d2524-220">Her sağlayıcı ve kategori çifti için en özel kural, kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="d2524-221">Belirli bir kategori için oluşturulan her sağlayıcı için aşağıdaki algoritma kullanılır `ILogger` :</span><span class="sxs-lookup"><span data-stu-id="d2524-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="d2524-222">Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="d2524-223">Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="d2524-224">Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="d2524-225">Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="d2524-226">Birden çok kural seçilirse, **son** olanı götürün.</span><span class="sxs-lookup"><span data-stu-id="d2524-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="d2524-227">Hiçbir kural seçilmezse, kullanın `MinimumLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="d2524-228">DotNet Run ve Visual Studio 'dan çıktıyı günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d2524-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="d2524-229">[Varsayılan günlük oluşturma sağlayıcılarıyla](#lp) oluşturulan Günlükler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d2524-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="d2524-230">Visual Studio’da</span><span class="sxs-lookup"><span data-stu-id="d2524-230">In Visual Studio</span></span>
  * <span data-ttu-id="d2524-231">Hata ayıklama sırasında hata ayıkla çıkış penceresinde.</span><span class="sxs-lookup"><span data-stu-id="d2524-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="d2524-232">ASP.NET Core Web sunucusu penceresinde.</span><span class="sxs-lookup"><span data-stu-id="d2524-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="d2524-233">Konsol penceresinde, uygulama ile birlikte çalıştırıldığında `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d2524-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="d2524-234">"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır.</span><span class="sxs-lookup"><span data-stu-id="d2524-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="d2524-235">ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="d2524-236">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="d2524-236">Log category</span></span>

<span data-ttu-id="d2524-237">Bir `ILogger` nesne oluşturulduğunda, bir *Kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="d2524-238">Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="d2524-239">Kategori dizesi rastgele, ancak kural sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d2524-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="d2524-240">Örneğin, bir denetleyicide adı olabilir `"TodoApi.Controllers.TodoController"` .</span><span class="sxs-lookup"><span data-stu-id="d2524-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="d2524-241">ASP.NET Core Web Apps, `ILogger<T>` `ILogger` Kategori olarak tam nitelikli tür adını kullanan bir örneği otomatik olarak almak için kullanır `T` :</span><span class="sxs-lookup"><span data-stu-id="d2524-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="d2524-242">Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="d2524-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="d2524-243">`ILogger<T>`, `CreateLogger` tam nitelikli tür adı ile çağırma ile eşdeğerdir `T` .</span><span class="sxs-lookup"><span data-stu-id="d2524-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="d2524-244">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="d2524-244">Log level</span></span>

<span data-ttu-id="d2524-245">Aşağıdaki tabloda <xref:Microsoft.Extensions.Logging.LogLevel> değerler, kolaylık `Log{LogLevel}` genişletme yöntemi ve önerilen kullanım listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="d2524-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="d2524-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d2524-246">LogLevel</span></span> | <span data-ttu-id="d2524-247">Değer</span><span class="sxs-lookup"><span data-stu-id="d2524-247">Value</span></span> | <span data-ttu-id="d2524-248">Yöntem</span><span class="sxs-lookup"><span data-stu-id="d2524-248">Method</span></span> | <span data-ttu-id="d2524-249">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="d2524-250">İzleme</span><span class="sxs-lookup"><span data-stu-id="d2524-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-251">0</span><span class="sxs-lookup"><span data-stu-id="d2524-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="d2524-252">En ayrıntılı iletileri içerir.</span><span class="sxs-lookup"><span data-stu-id="d2524-252">Contain the most detailed messages.</span></span> <span data-ttu-id="d2524-253">Bu iletilerde hassas uygulama verileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="d2524-254">Bu iletiler varsayılan olarak devre dışıdır ve üretimde ***etkinleştirilmemelidir.***</span><span class="sxs-lookup"><span data-stu-id="d2524-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="d2524-255">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-256">1</span><span class="sxs-lookup"><span data-stu-id="d2524-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="d2524-257">Hata ayıklama ve geliştirme için.</span><span class="sxs-lookup"><span data-stu-id="d2524-257">For debugging and development.</span></span> <span data-ttu-id="d2524-258">Yüksek hacimden dolayı üretimde dikkatli olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="d2524-259">Bilgi</span><span class="sxs-lookup"><span data-stu-id="d2524-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-260">2</span><span class="sxs-lookup"><span data-stu-id="d2524-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="d2524-261">Uygulamanın genel akışını izler.</span><span class="sxs-lookup"><span data-stu-id="d2524-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="d2524-262">Uzun süreli bir değere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-262">May have long-term value.</span></span> |
| [<span data-ttu-id="d2524-263">Uyarı</span><span class="sxs-lookup"><span data-stu-id="d2524-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-264">3</span><span class="sxs-lookup"><span data-stu-id="d2524-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="d2524-265">Olağandışı veya beklenmeyen olaylar için.</span><span class="sxs-lookup"><span data-stu-id="d2524-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="d2524-266">Genellikle, uygulamanın başarısız olmasına neden olmayan hataları veya koşulları içerir.</span><span class="sxs-lookup"><span data-stu-id="d2524-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="d2524-267">Hata</span><span class="sxs-lookup"><span data-stu-id="d2524-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-268">4</span><span class="sxs-lookup"><span data-stu-id="d2524-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="d2524-269">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="d2524-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="d2524-270">Bu iletiler, uygulama genelinde bir hata değil, geçerli işlemde veya istekte bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="d2524-271">Kritik</span><span class="sxs-lookup"><span data-stu-id="d2524-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-272">5</span><span class="sxs-lookup"><span data-stu-id="d2524-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="d2524-273">Anında ilgilenilmesi gereken hatalarda.</span><span class="sxs-lookup"><span data-stu-id="d2524-273">For failures that require immediate attention.</span></span> <span data-ttu-id="d2524-274">Örnekler: veri kaybı senaryoları, disk alanı yetersiz.</span><span class="sxs-lookup"><span data-stu-id="d2524-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="d2524-275">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="d2524-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d2524-276">6</span><span class="sxs-lookup"><span data-stu-id="d2524-276">6</span></span> | | <span data-ttu-id="d2524-277">Günlüğe kaydetme kategorisinin herhangi bir ileti yazmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="d2524-278">Önceki tabloda, `LogLevel` En düşük önem derecesine göre listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="d2524-279">[Günlük](xref:Microsoft.Extensions.Logging.LoggerExtensions) yönteminin ilk parametresi, <xref:Microsoft.Extensions.Logging.LogLevel> Günlüğün önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="d2524-280">Çağırmak yerine `Log(LogLevel, ...)` , çoğu Geliştirici [günlük {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) uzantı yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="d2524-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="d2524-281">`Log{LogLevel}`Uzantı yöntemleri [günlük yöntemini çağırır ve LogLevel değerini belirtir](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="d2524-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="d2524-282">Örneğin, aşağıdaki iki günlük çağrısı işlevsel olarak eşdeğerdir ve aynı günlüğü üretir:</span><span class="sxs-lookup"><span data-stu-id="d2524-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="d2524-283">`MyLogEvents.TestItem`olay KIMLIĞIDIR.</span><span class="sxs-lookup"><span data-stu-id="d2524-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="d2524-284">`MyLogEvents`, örnek uygulamanın bir parçasıdır ve [günlük olay kimliği](#leid) bölümünde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d2524-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="d2524-285">Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:</span><span class="sxs-lookup"><span data-stu-id="d2524-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="d2524-286">Yukarıdaki kodda, ilk `Log{LogLevel}` parametresi `MyLogEvents.GetItem` [günlük olay kimliğidir](#leid).</span><span class="sxs-lookup"><span data-stu-id="d2524-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="d2524-287">İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="d2524-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="d2524-288">Yöntem parametreleri bu belgenin ilerleyen kısımlarında bulunan [ileti şablonu](#lmt) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d2524-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="d2524-289">`Log{LogLevel}`Belirli bir depolama ortamına ne kadar günlük çıktısı yazıldığını denetlemek için uygun yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="d2524-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="d2524-290">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d2524-290">For example:</span></span>

* <span data-ttu-id="d2524-291">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="d2524-291">In production:</span></span>
  * <span data-ttu-id="d2524-292">Veya düzeylerinde günlüğe kaydetme, `Trace` `Information` yüksek hacimli ayrıntılı günlük iletileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d2524-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="d2524-293">Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, `Trace` `Information` iletileri yüksek hacimli ve düşük maliyetli bir veri deposuna günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d2524-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="d2524-294">`Trace`Belirli kategorileri ve sınırlamayı değerlendirin `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="d2524-295">Düzeylerde oturum açma, `Warning` `Critical` birkaç günlük iletisi üretmelidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="d2524-296">Maliyetler ve depolama sınırları genellikle bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="d2524-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="d2524-297">Birkaç günlük veri deposu seçimlerine daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="d2524-298">Geliştirme aşamasında:</span><span class="sxs-lookup"><span data-stu-id="d2524-298">In development:</span></span>
  * <span data-ttu-id="d2524-299">`Warning` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="d2524-300">`Trace` `Information` Sorun giderirken veya ileti ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="d2524-301">Çıktıyı sınırlandırmak için, `Trace` `Information` yalnızca araştırma bölümündeki Kategoriler için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="d2524-302">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="d2524-303">Örneğin, için günlük çıktısını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d2524-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="d2524-304">RazorASP.NET Core şablonlarıyla oluşturulan bir sayfalar uygulaması.</span><span class="sxs-lookup"><span data-stu-id="d2524-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="d2524-305">Günlük kayıt kümesi`Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="d2524-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="d2524-306">Gizlilik sayfasına gidilme:</span><span class="sxs-lookup"><span data-stu-id="d2524-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="d2524-307">Aşağıdaki JSON kümeleri `Logging:Console:LogLevel:Microsoft:Information` :</span><span class="sxs-lookup"><span data-stu-id="d2524-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="d2524-308">Günlüğe olay KIMLIĞI</span><span class="sxs-lookup"><span data-stu-id="d2524-308">Log event ID</span></span>

<span data-ttu-id="d2524-309">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="d2524-310">Örnek uygulama, `MyLogEvents` olay kimliklerini tanımlamak için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="d2524-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="d2524-311">Olay KIMLIĞI bir olay kümesini ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="d2524-311">An event ID associates a set of events.</span></span> <span data-ttu-id="d2524-312">Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="d2524-313">Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="d2524-314">Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor.</span><span class="sxs-lookup"><span data-stu-id="d2524-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="d2524-315">Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:</span><span class="sxs-lookup"><span data-stu-id="d2524-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="d2524-316">Bazı günlük sağlayıcıları olay KIMLIĞINI bir alanda depolar, bu da KIMLIĞE filtrelemeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="d2524-317">Günlük iletisi şablonu</span><span class="sxs-lookup"><span data-stu-id="d2524-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="d2524-318">Her günlük API 'SI bir ileti şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-318">Each log API uses a message template.</span></span> <span data-ttu-id="d2524-319">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="d2524-320">Sayılar değil, yer tutucular için adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="d2524-321">Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası.</span><span class="sxs-lookup"><span data-stu-id="d2524-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="d2524-322">Aşağıdaki kodda, parametre adları ileti şablonunda sıra dışında:</span><span class="sxs-lookup"><span data-stu-id="d2524-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="d2524-323">Yukarıdaki kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d2524-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="d2524-324">Bu yaklaşım, günlük sağlayıcılarının [anlam veya yapılandırılmış günlük kaydı](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)uygulamasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="d2524-325">Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="d2524-326">Bu, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="d2524-327">Örneğin, aşağıdaki günlükçü yöntemini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d2524-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="d2524-328">Örneğin, Azure Tablo depolama alanına oturum açarken:</span><span class="sxs-lookup"><span data-stu-id="d2524-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="d2524-329">Her Azure Tablo varlığı `ID` ve özellikleri olabilir `RequestTime` .</span><span class="sxs-lookup"><span data-stu-id="d2524-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="d2524-330">Özellikleri olan tablolar günlüğe kaydedilen verilerde sorguları basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="d2524-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="d2524-331">Örneğin, bir sorgu belirli bir aralıktaki tüm günlükleri, `RequestTime` kısa mesajdaki zaman aşımını ayrıştırmadan bulabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="d2524-332">Günlük özel durumları</span><span class="sxs-lookup"><span data-stu-id="d2524-332">Log exceptions</span></span>

<span data-ttu-id="d2524-333">Günlükçü yöntemlerinde bir özel durum parametresi alan aşırı yüklemeleri var:</span><span class="sxs-lookup"><span data-stu-id="d2524-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="d2524-334">Özel durum günlüğe kaydetme, sağlayıcıya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="d2524-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="d2524-335">Varsayılan günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="d2524-335">Default log level</span></span>

<span data-ttu-id="d2524-336">Varsayılan günlük düzeyi ayarlanmamışsa varsayılan günlük düzeyi değeri olur `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="d2524-337">Örneğin, aşağıdaki Web uygulamasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d2524-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="d2524-338">ASP.NET Web uygulaması şablonlarıyla oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d2524-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="d2524-339">*appsettings.js* ve *appsettings.Development.js* silinmiş ya da yeniden adlandırıldı.</span><span class="sxs-lookup"><span data-stu-id="d2524-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="d2524-340">Önceki kurulumla, gizlilik veya giriş sayfasına gidildiğinde `Trace` `Debug` Kategori adında birçok, ve `Information` ile ileti üretilir `Microsoft` .</span><span class="sxs-lookup"><span data-stu-id="d2524-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="d2524-341">Aşağıdaki kod, varsayılan günlük düzeyi yapılandırmada ayarlanmamışsa varsayılan günlük düzeyini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d2524-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="d2524-342">Genellikle, kod değil yapılandırmada günlük düzeyleri belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="d2524-343">Filter işlevi</span><span class="sxs-lookup"><span data-stu-id="d2524-343">Filter function</span></span>

<span data-ttu-id="d2524-344">Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır:</span><span class="sxs-lookup"><span data-stu-id="d2524-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="d2524-345">Önceki kod, kategori içerdiğinde `Controller` veya `Microsoft` ve günlük düzeyi `Information` veya daha yüksek olduğunda konsol günlüklerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="d2524-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="d2524-346">Genellikle, kod değil yapılandırmada günlük düzeyleri belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="d2524-347">ASP.NET Core ve EF Core kategorileri</span><span class="sxs-lookup"><span data-stu-id="d2524-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="d2524-348">Aşağıdaki tabloda, ASP.NET Core ve Entity Framework Core tarafından kullanılan ve Günlükler hakkındaki notlarla ilgili bazı kategoriler yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="d2524-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="d2524-349">Kategori</span><span class="sxs-lookup"><span data-stu-id="d2524-349">Category</span></span>                            | <span data-ttu-id="d2524-350">Notlar</span><span class="sxs-lookup"><span data-stu-id="d2524-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="d2524-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="d2524-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="d2524-352">Genel ASP.NET Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="d2524-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="d2524-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="d2524-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="d2524-354">Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı.</span><span class="sxs-lookup"><span data-stu-id="d2524-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="d2524-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="d2524-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="d2524-356">İzin verilen konaklar.</span><span class="sxs-lookup"><span data-stu-id="d2524-356">Hosts allowed.</span></span> |
| <span data-ttu-id="d2524-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="d2524-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="d2524-358">HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları.</span><span class="sxs-lookup"><span data-stu-id="d2524-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="d2524-359">Hangi barındırma başlangıç derlemeleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="d2524-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="d2524-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="d2524-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="d2524-361">MVC ve Razor Tanılama.</span><span class="sxs-lookup"><span data-stu-id="d2524-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="d2524-362">Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="d2524-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="d2524-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="d2524-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="d2524-364">Eşleşen bilgileri yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="d2524-364">Route matching information.</span></span> |
| <span data-ttu-id="d2524-365">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="d2524-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="d2524-366">Bağlantı başlatın, durdurun ve canlı yanıtları koruyun.</span><span class="sxs-lookup"><span data-stu-id="d2524-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="d2524-367">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="d2524-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="d2524-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="d2524-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="d2524-369">Sunulan dosyalar.</span><span class="sxs-lookup"><span data-stu-id="d2524-369">Files served.</span></span> |
| <span data-ttu-id="d2524-370">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d2524-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="d2524-371">Genel Entity Framework Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="d2524-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="d2524-372">Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="d2524-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="d2524-373">Konsol penceresinde daha fazla kategori görüntülemek için **appsettings.Development.js** aşağıdaki şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="d2524-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="d2524-374">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="d2524-374">Log scopes</span></span>

 <span data-ttu-id="d2524-375">*Kapsam* bir mantıksal işlemler kümesini gruplandırabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="d2524-376">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="d2524-377">Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="d2524-378">Kapsam:</span><span class="sxs-lookup"><span data-stu-id="d2524-378">A scope:</span></span>

* <span data-ttu-id="d2524-379"><xref:System.IDisposable>, Yöntemi tarafından döndürülen türdür <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> .</span><span class="sxs-lookup"><span data-stu-id="d2524-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="d2524-380">Atılana kadar sürer.</span><span class="sxs-lookup"><span data-stu-id="d2524-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="d2524-381">Aşağıdaki sağlayıcılar kapsamları destekler:</span><span class="sxs-lookup"><span data-stu-id="d2524-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="d2524-382">AzureAppServicesFile ve AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d2524-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="d2524-383">Bir blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın `using` :</span><span class="sxs-lookup"><span data-stu-id="d2524-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="d2524-384">Aşağıdaki JSON konsol sağlayıcısı için kapsamları etkinleştirdi:</span><span class="sxs-lookup"><span data-stu-id="d2524-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="d2524-385">Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="d2524-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="d2524-386">Genellikle günlüğe kaydetme, kodda değil yapılandırmada belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="d2524-387">Yerleşik günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-387">Built-in logging providers</span></span>

<span data-ttu-id="d2524-388">ASP.NET Core aşağıdaki günlük sağlayıcılarını içerir:</span><span class="sxs-lookup"><span data-stu-id="d2524-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="d2524-389">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-389">Console</span></span>](#console)
* [<span data-ttu-id="d2524-390">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-390">Debug</span></span>](#debug)
* [<span data-ttu-id="d2524-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="d2524-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="d2524-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="d2524-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="d2524-393">AzureAppServicesFile ve AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d2524-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="d2524-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d2524-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="d2524-395">`stdout`ASP.NET Core modülüyle günlüğe kaydetme ve hata ayıklama ile ilgili bilgi için bkz <xref:test/troubleshoot-azure-iis> <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> . ve.</span><span class="sxs-lookup"><span data-stu-id="d2524-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="d2524-396">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-396">Console</span></span>

<span data-ttu-id="d2524-397">`Console`Sağlayıcı çıktıyı konsola kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d2524-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="d2524-398">Geliştirme sırasında günlükleri görüntüleme hakkında daha fazla bilgi için `Console` bkz. [DotNet Run ve Visual Studio 'Dan çıktıyı günlüğe kaydetme](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="d2524-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="d2524-399">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-399">Debug</span></span>

<span data-ttu-id="d2524-400">`Debug`Sağlayıcı, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="d2524-401">`System.Diagnostics.Debug.WriteLine`Sağlayıcıya yazmak için çağrılar `Debug` .</span><span class="sxs-lookup"><span data-stu-id="d2524-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="d2524-402">Linux 'ta, `Debug` sağlayıcı günlük konumu dağıtıma bağımlıdır ve aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="d2524-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="d2524-403">*/var/log/Message*</span><span class="sxs-lookup"><span data-stu-id="d2524-403">*/var/log/message*</span></span>
* <span data-ttu-id="d2524-404">*/var/log/Syslog*</span><span class="sxs-lookup"><span data-stu-id="d2524-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="d2524-405">Olay Kaynağı</span><span class="sxs-lookup"><span data-stu-id="d2524-405">Event Source</span></span>

<span data-ttu-id="d2524-406">`EventSource`Sağlayıcı, adlı platformlar arası bir olay kaynağına Yazar `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="d2524-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="d2524-407">Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="d2524-408">DotNet izleme araçları</span><span class="sxs-lookup"><span data-stu-id="d2524-408">dotnet trace tooling</span></span>

<span data-ttu-id="d2524-409">[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) Aracı, çalışan bir Işlemin .NET Core izlemelerinin toplanmasını sağlayan platformlar arası CLI genel aracıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="d2524-410">Araç, <xref:Microsoft.Extensions.Logging.EventSource> kullanarak sağlayıcı verilerini toplar <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .</span><span class="sxs-lookup"><span data-stu-id="d2524-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="d2524-411">Bkz. yükleme yönergeleri için [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) .</span><span class="sxs-lookup"><span data-stu-id="d2524-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="d2524-412">Bir uygulamadan izleme toplamak için DotNet Trace araçları kullanın:</span><span class="sxs-lookup"><span data-stu-id="d2524-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="d2524-413">Komutuyla uygulamayı çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d2524-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="d2524-414">.NET Core uygulamasının işlem tanımlayıcısını (PID) belirleme:</span><span class="sxs-lookup"><span data-stu-id="d2524-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="d2524-415">Windows 'ta aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="d2524-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="d2524-416">Görev Yöneticisi (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="d2524-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="d2524-417">Tasklist komutu</span><span class="sxs-lookup"><span data-stu-id="d2524-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="d2524-418">Get-Process PowerShell komutu</span><span class="sxs-lookup"><span data-stu-id="d2524-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="d2524-419">Linux 'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="d2524-420">Uygulamanın derlemesi ile aynı ada sahip olan işlem için PID 'i bulun.</span><span class="sxs-lookup"><span data-stu-id="d2524-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="d2524-421">Komutunu yürütün `dotnet trace` .</span><span class="sxs-lookup"><span data-stu-id="d2524-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="d2524-422">Genel komut sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="d2524-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="d2524-423">PowerShell komut kabuğu kullanırken, `--providers` değeri tek tırnak işaretleri () içine alın `'` :</span><span class="sxs-lookup"><span data-stu-id="d2524-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="d2524-424">Windows dışı platformlarda, `-f speedscope` Çıkış izleme dosyasının biçimini olarak değiştirme seçeneğini ekleyin `speedscope` .</span><span class="sxs-lookup"><span data-stu-id="d2524-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="d2524-425">Sözcükle</span><span class="sxs-lookup"><span data-stu-id="d2524-425">Keyword</span></span> | <span data-ttu-id="d2524-426">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="d2524-427">1</span><span class="sxs-lookup"><span data-stu-id="d2524-427">1</span></span>       | <span data-ttu-id="d2524-428">İçin meta olayları günlüğe kaydedin `LoggingEventSource` .</span><span class="sxs-lookup"><span data-stu-id="d2524-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="d2524-429">Olayları günlüğe kaydetmez `ILogger` ).</span><span class="sxs-lookup"><span data-stu-id="d2524-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="d2524-430">2</span><span class="sxs-lookup"><span data-stu-id="d2524-430">2</span></span>       | <span data-ttu-id="d2524-431">`Message`Çağrıldığında olayı açar `ILogger.Log()` .</span><span class="sxs-lookup"><span data-stu-id="d2524-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d2524-432">Programlı (biçimlendirilmedi) bir şekilde bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="d2524-433">4</span><span class="sxs-lookup"><span data-stu-id="d2524-433">4</span></span>       | <span data-ttu-id="d2524-434">`FormatMessage`Çağrıldığında olayı açar `ILogger.Log()` .</span><span class="sxs-lookup"><span data-stu-id="d2524-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d2524-435">, Bilgilerin biçimlendirilen dize sürümünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="d2524-436">8</span><span class="sxs-lookup"><span data-stu-id="d2524-436">8</span></span>       | <span data-ttu-id="d2524-437">`MessageJson`Çağrıldığında olayı açar `ILogger.Log()` .</span><span class="sxs-lookup"><span data-stu-id="d2524-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d2524-438">Bağımsız değişkenlerin JSON gösterimini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="d2524-439">Olay düzeyi</span><span class="sxs-lookup"><span data-stu-id="d2524-439">Event Level</span></span> | <span data-ttu-id="d2524-440">Description</span><span class="sxs-lookup"><span data-stu-id="d2524-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="d2524-441">0</span><span class="sxs-lookup"><span data-stu-id="d2524-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="d2524-442">1</span><span class="sxs-lookup"><span data-stu-id="d2524-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="d2524-443">2</span><span class="sxs-lookup"><span data-stu-id="d2524-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="d2524-444">3</span><span class="sxs-lookup"><span data-stu-id="d2524-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="d2524-445">4</span><span class="sxs-lookup"><span data-stu-id="d2524-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="d2524-446">5</span><span class="sxs-lookup"><span data-stu-id="d2524-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="d2524-447">`FilterSpecs`için girdiler `{Logger Category}` ve `{Event Level}` ek günlük filtreleme koşullarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d2524-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="d2524-448">`FilterSpecs`Girişleri noktalı virgül () ile ayırın `;` .</span><span class="sxs-lookup"><span data-stu-id="d2524-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="d2524-449">Bir Windows komut kabuğu (değer etrafında tek tırnak**olmadan** ) ile örnek `--providers` :</span><span class="sxs-lookup"><span data-stu-id="d2524-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="d2524-450">Yukarıdaki komut şunları etkinleştirir:</span><span class="sxs-lookup"><span data-stu-id="d2524-450">The preceding command activates:</span></span>

   * <span data-ttu-id="d2524-451">`4`Hatalar () için biçimlendirilen dizeler () üretmek üzere olay kaynağı günlükçüsü `2` .</span><span class="sxs-lookup"><span data-stu-id="d2524-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="d2524-452">`Microsoft.AspNetCore.Hosting`günlüğe kaydetme `Informational` düzeyinde () günlüğe kaydetme `4` .</span><span class="sxs-lookup"><span data-stu-id="d2524-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="d2524-453">ENTER tuşuna veya CTRL + C tuşlarına basarak DotNet izleme araçlarını durdurun.</span><span class="sxs-lookup"><span data-stu-id="d2524-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="d2524-454">İzleme, komutun yürütüldüğü klasörde *Trace. NetTrace* adıyla kaydedilir `dotnet trace` .</span><span class="sxs-lookup"><span data-stu-id="d2524-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="d2524-455">Trace 'i [PerfView](#perfview)ile açın.</span><span class="sxs-lookup"><span data-stu-id="d2524-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="d2524-456">*Trace. NetTrace* dosyasını açın ve izleme olaylarını araştırın.</span><span class="sxs-lookup"><span data-stu-id="d2524-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="d2524-457">Uygulama Konağı ile derlenmezse `CreateDefaultBuilder` , [olay kaynak sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="d2524-458">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="d2524-458">For more information, see:</span></span>

* <span data-ttu-id="d2524-459">[Performans Analizi yardımcı programı Için izleme (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core belgeleri)</span><span class="sxs-lookup"><span data-stu-id="d2524-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="d2524-460">[Performans Analizi yardımcı programı (DotNet-Trace) Için izleme](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (DotNet/Diagnostics GitHub deposu belgeleri)</span><span class="sxs-lookup"><span data-stu-id="d2524-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="d2524-461">[Loggingeventsource sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API tarayıcısı)</span><span class="sxs-lookup"><span data-stu-id="d2524-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="d2524-462">[Loggingeventsource başvuru kaynağı (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): farklı bir sürüm için başvuru kaynağı almak için dalı olarak değiştirin `release/{Version}` , burada `{Version}` ASP.NET Core sürümü istenen sürümdür.</span><span class="sxs-lookup"><span data-stu-id="d2524-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="d2524-463">[PerfView](#perfview): olay kaynağı izlemelerini görüntülemek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="d2524-464">PerfView</span><span class="sxs-lookup"><span data-stu-id="d2524-464">Perfview</span></span>

<span data-ttu-id="d2524-465">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="d2524-466">ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="d2524-467">Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="d2524-468">`*`Dizenin başlangıcında kaçırmayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="d2524-469">Windows olay günlüğü</span><span class="sxs-lookup"><span data-stu-id="d2524-469">Windows EventLog</span></span>

<span data-ttu-id="d2524-470">`EventLog`Sağlayıcı, Windows olay günlüğüne günlük çıktısı gönderir.</span><span class="sxs-lookup"><span data-stu-id="d2524-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="d2524-471">Diğer sağlayıcılardan farklı olarak, `EventLog` sağlayıcı varsayılan sağlayıcı ***not*** olmayan ayarları uygulamaz.</span><span class="sxs-lookup"><span data-stu-id="d2524-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="d2524-472">`EventLog`Günlük ayarları belirtilmemişse, [Varsayılan olarak LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103)' ı kullanırlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="d2524-473">Olayları daha düşük günlüğe kaydetmek için <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , günlük düzeyini açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="d2524-474">Aşağıdaki örnek, olay günlüğü varsayılan günlük düzeyini şu şekilde ayarlar <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d2524-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="d2524-475">[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) geçiş yapabilir <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="d2524-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="d2524-476">`null`Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d2524-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="d2524-477">`LogName`: "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="d2524-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="d2524-478">`SourceName`: ".NET çalışma zamanı"</span><span class="sxs-lookup"><span data-stu-id="d2524-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="d2524-479">`MachineName`: Yerel makine adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d2524-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="d2524-480">Aşağıdaki kod, ' `SourceName` nin varsayılan değerinden öğesini olarak değiştirir `".NET Runtime"` `MyLogs` :</span><span class="sxs-lookup"><span data-stu-id="d2524-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="d2524-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d2524-481">Azure App Service</span></span>

<span data-ttu-id="d2524-482">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="d2524-483">Sağlayıcı paketi, paylaşılan çerçeveye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="d2524-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="d2524-484">Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="d2524-485">Sağlayıcı ayarlarını yapılandırmak için <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> Aşağıdaki örnekte gösterildiği gibi ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="d2524-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="d2524-486">Uygulama Azure App Service dağıtıldığında, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) bölümündeki ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="d2524-487">Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="d2524-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="d2524-488">**Uygulama günlüğü (dosya sistemi)**</span><span class="sxs-lookup"><span data-stu-id="d2524-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="d2524-489">**Uygulama günlüğü (blob)**</span><span class="sxs-lookup"><span data-stu-id="d2524-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="d2524-490">Günlük dosyaları için varsayılan konum *D: \\ Home \\ LogFiles \\ uygulama* klasöründedir ve varsayılan dosya adı *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="d2524-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="d2524-491">Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d2524-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="d2524-492">Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid} -applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="d2524-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="d2524-493">Bu sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="d2524-494">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="d2524-494">Azure log streaming</span></span>

<span data-ttu-id="d2524-495">Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemeyi destekler:</span><span class="sxs-lookup"><span data-stu-id="d2524-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="d2524-496">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="d2524-496">The app server</span></span>
* <span data-ttu-id="d2524-497">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="d2524-497">The web server</span></span>
* <span data-ttu-id="d2524-498">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="d2524-498">Failed request tracing</span></span>

<span data-ttu-id="d2524-499">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="d2524-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="d2524-500">Uygulamanın Portal sayfasından **App Service günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d2524-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="d2524-501">**Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="d2524-502">Günlük **düzeyini**seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-502">Choose the log **Level**.</span></span> <span data-ttu-id="d2524-503">Bu ayar yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="d2524-504">Günlükleri görüntülemek için **günlük akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d2524-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="d2524-505">Günlüğe kaydedilen iletiler arabirimiyle günlüğe kaydedilir `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="d2524-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="d2524-506">Azure Application Insights</span></span>

<span data-ttu-id="d2524-507">[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri [Azure Application Insights](/azure/azure-monitor/app/cloudservices)yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="d2524-508">Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="d2524-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="d2524-509">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d2524-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="d2524-510">Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="d2524-511">Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.</span><span class="sxs-lookup"><span data-stu-id="d2524-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="d2524-512">[Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketi, ASP.NET Core değil, ASP.NET 4. x içindir.</span><span class="sxs-lookup"><span data-stu-id="d2524-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="d2524-513">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="d2524-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="d2524-514">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="d2524-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="d2524-515">[ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="d2524-516">[.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="d2524-517">[Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="d2524-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="d2524-518">Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .</span><span class="sxs-lookup"><span data-stu-id="d2524-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="d2524-519">Üçüncü taraf günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-519">Third-party logging providers</span></span>

<span data-ttu-id="d2524-520">ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="d2524-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="d2524-521">[ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="d2524-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="d2524-523">[Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="d2524-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="d2524-524">[KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="d2524-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="d2524-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="d2524-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="d2524-526">[Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="d2524-527">[NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="d2524-528">[Pgünlükçü](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub deposu](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="d2524-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="d2524-529">[Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d2524-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="d2524-530">[Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="d2524-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="d2524-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d2524-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="d2524-532">Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d2524-533">Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="d2524-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="d2524-534">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="d2524-535">`ILoggerFactory`Günlüğe kaydetme çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="d2524-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="d2524-536">Daha fazla bilgi için bkz. her sağlayıcının belgeleri.</span><span class="sxs-lookup"><span data-stu-id="d2524-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="d2524-537">Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d2524-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="d2524-538">Konak olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="d2524-538">Non-host console app</span></span>

<span data-ttu-id="d2524-539">Genel konağın Web 'de olmayan bir uygulamada nasıl kullanılacağına ilişkin bir örnek için, [arka plan görevlerinin örnek uygulaması](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) () *program.cs* dosyasına bakın <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="d2524-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="d2524-540">Genel ana bilgisayarı olmayan uygulamalar için günlük kodu, [sağlayıcıların Eklenme](#add-providers) ve [günlükçülerin oluşturulma](#create-logs)biçiminde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="d2524-541">Günlüğe kaydetme sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-541">Logging providers</span></span>

<span data-ttu-id="d2524-542">Konak olmayan bir konsol uygulamasında, `Add{provider name}` oluşturma sırasında sağlayıcının uzantı yöntemini çağırın `LoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="d2524-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="d2524-543">Günlükleri oluştur</span><span class="sxs-lookup"><span data-stu-id="d2524-543">Create logs</span></span>

<span data-ttu-id="d2524-544">Günlükler oluşturmak için bir nesnesi kullanın <xref:Microsoft.Extensions.Logging.ILogger%601> .</span><span class="sxs-lookup"><span data-stu-id="d2524-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="d2524-545">`LoggerFactory`Oluşturmak için öğesini kullanın `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="d2524-546">Aşağıdaki örnek `LoggingConsoleApp.Program` , kategorisi olarak bir günlükçü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d2524-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="d2524-547">Aşağıdaki ASP.NET CORE örneklerinde, günlükçü, düzeyi olan Günlükler oluşturmak için kullanılır `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="d2524-548">Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="d2524-549">[Düzeyler](#log-level) ve [Kategoriler](#log-category) bu belgede daha ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d2524-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="d2524-550">Konak oluşturma sırasında günlüğe kaydet</span><span class="sxs-lookup"><span data-stu-id="d2524-550">Log during host construction</span></span>

<span data-ttu-id="d2524-551">Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d2524-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="d2524-552">Ancak, ayrı bir günlükçü kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-552">However, a separate logger can be used.</span></span> <span data-ttu-id="d2524-553">Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d2524-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="d2524-554">`AddSerilog`, içinde belirtilen statik yapılandırmayı kullanır `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="d2524-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="d2524-555">ILogger 'a bağlı bir hizmet yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d2524-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="d2524-556">`Startup`Web ana bilgisayarı için ayrı bır dı kapsayıcısı oluşturulduğundan, bir günlükçü 'nin önceki ASP.NET Core sürümlerinde çalışacak şekilde bir günlükçü ekleme.</span><span class="sxs-lookup"><span data-stu-id="d2524-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="d2524-557">Genel ana bilgisayar için yalnızca bir kapsayıcı oluşturma hakkında daha fazla bilgi için bkz. [Son değişiklik duyurusu](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="d2524-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="d2524-558">Bağımlı olan bir hizmeti yapılandırmak için `ILogger<T>` , Oluşturucu ekleme veya bir fabrika yöntemi sağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="d2524-559">Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="d2524-560">Örneğin, DI tarafından sağlanmış bir örneğe ihtiyacı olan bir hizmeti düşünün `ILogger<T>` :</span><span class="sxs-lookup"><span data-stu-id="d2524-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="d2524-561">Önceki vurgulanmış kod, ilk kez [Func](/dotnet/api/system.func-2) , bir örneği oluşturmak IÇIN, dı kapsayıcısının bir örneğini oluşturmak için gereken bir Func `MyService` .</span><span class="sxs-lookup"><span data-stu-id="d2524-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="d2524-562">Kayıtlı hizmetlerden herhangi birine bu şekilde erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d2524-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="d2524-563">Ana oturum oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-563">Create logs in Main</span></span>

<span data-ttu-id="d2524-564">Aşağıdaki kod, `Main` `ILogger` konak oluşturulduktan sonra bir örneğinden bir örnek alarak oturum açar:</span><span class="sxs-lookup"><span data-stu-id="d2524-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="d2524-565">Başlangıçta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-565">Create logs in Startup</span></span>

<span data-ttu-id="d2524-566">Aşağıdaki kod günlükleri içinde yazar `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d2524-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="d2524-567">Yöntemde dı kapsayıcı kurulumu tamamlanmadan önce Günlüklerin yazılması `Startup.ConfigureServices` desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="d2524-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="d2524-568">Oluşturucuya günlükçü ekleme `Startup` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="d2524-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="d2524-569">Yöntem imzasına günlükçü ekleme `Startup.ConfigureServices` desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="d2524-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="d2524-570">Bu kısıtlamanın nedeni, günlük kaydının dı ve yapılandırmaya göre değişir ve bu da, ara ' ya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="d2524-571">Dı kapsayıcısı bitene kadar ayarlanamaz `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2524-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="d2524-572">`ILogger<T>`Bir günlükçü 'nin daha önceki sürümlerde çalışma alanına bağlı olan veya neden Oluşturucu ekleme hakkında bilgi için `Startup` , bkz. [ILogger 'a bağımlı olan bir hizmeti yapılandırma](#csdi)</span><span class="sxs-lookup"><span data-stu-id="d2524-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="d2524-573">Zaman uyumsuz günlükçü yöntemi yok</span><span class="sxs-lookup"><span data-stu-id="d2524-573">No asynchronous logger methods</span></span>

<span data-ttu-id="d2524-574">Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="d2524-575">Günlüğe kaydetme veri deposu yavaşsa doğrudan yazma.</span><span class="sxs-lookup"><span data-stu-id="d2524-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="d2524-576">Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı ve sonra bunları yavaş depoya daha sonra taşımayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="d2524-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="d2524-577">Örneğin, SQL Server oturum açarken `Log` Yöntemler zaman uyumlu olduğundan doğrudan bir yöntemde yapmayın `Log` .</span><span class="sxs-lookup"><span data-stu-id="d2524-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="d2524-578">Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin.</span><span class="sxs-lookup"><span data-stu-id="d2524-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="d2524-579">Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="d2524-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="d2524-580">Çalışan bir uygulamadaki günlük düzeylerini değiştirme</span><span class="sxs-lookup"><span data-stu-id="d2524-580">Change log levels in a running app</span></span>

<span data-ttu-id="d2524-581">Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez.</span><span class="sxs-lookup"><span data-stu-id="d2524-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="d2524-582">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="d2524-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="d2524-583">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), günlük yapılandırmasını varsayılan olarak yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="d2524-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="d2524-584">Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="d2524-585">ILogger ve ıloggerfactory</span><span class="sxs-lookup"><span data-stu-id="d2524-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="d2524-586"><xref:Microsoft.Extensions.Logging.ILogger%601>Ve <xref:Microsoft.Extensions.Logging.ILoggerFactory> arabirimleri ve uygulamaları .NET Core SDK dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="d2524-587">Bunlar ayrıca aşağıdaki NuGet paketlerinde de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d2524-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="d2524-588">Arabirimler [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="d2524-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="d2524-589">Varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)' dir.</span><span class="sxs-lookup"><span data-stu-id="d2524-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="d2524-590">Koddaki günlük filtresi kurallarını Uygula</span><span class="sxs-lookup"><span data-stu-id="d2524-590">Apply log filter rules in code</span></span>

<span data-ttu-id="d2524-591">Günlük filtresi kurallarını ayarlamak için tercih edilen yaklaşım [yapılandırma](xref:fundamentals/configuration/index)kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d2524-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="d2524-592">Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="d2524-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="d2524-593">`logging.AddFilter("System", LogLevel.Debug)``System`Kategori ve günlük düzeyini belirtir `Debug` .</span><span class="sxs-lookup"><span data-stu-id="d2524-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="d2524-594">Belirli bir sağlayıcı yapılandırılmadığı için filtre tüm sağlayıcılara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="d2524-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`belirtir</span><span class="sxs-lookup"><span data-stu-id="d2524-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="d2524-596">`Debug`Günlüğe kaydetme sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="d2524-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="d2524-597">Günlük düzeyi `Information` ve üzeri.</span><span class="sxs-lookup"><span data-stu-id="d2524-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="d2524-598">İle başlayan tüm kategoriler `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="d2524-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="d2524-599">Özel günlükçü oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-599">Create a custom logger</span></span>

<span data-ttu-id="d2524-600">Özel bir günlükçü eklemek için bir ile ekleyin <xref:Microsoft.Extensions.Logging.ILoggerProvider> <xref:Microsoft.Extensions.Logging.ILoggerFactory> :</span><span class="sxs-lookup"><span data-stu-id="d2524-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="d2524-601">, `ILoggerProvider` Bir veya daha fazla `ILogger` örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d2524-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="d2524-602">`ILogger`Örnekler, bilgileri günlüğe kaydetmek için Framework tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d2524-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="d2524-603">Örnek özel günlükçü yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d2524-603">Sample custom logger configuration</span></span>

<span data-ttu-id="d2524-604">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d2524-604">The sample:</span></span>

* <span data-ttu-id="d2524-605">, Olay KIMLIĞI ve günlük düzeyi tarafından günlük konsolunun rengini ayarlayan çok basit bir örnek olacak şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d2524-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="d2524-606">Günlükçüler genellikle olay KIMLIĞINE göre değişmez ve günlük düzeyine özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="d2524-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="d2524-607">Aşağıdaki yapılandırma türünü kullanarak günlük düzeyi ve olay KIMLIĞI başına farklı renk konsolu girdileri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d2524-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="d2524-608">Yukarıdaki kod, varsayılan düzeyi olarak `Warning` ' i ve rengini olarak ayarlar `Yellow` .</span><span class="sxs-lookup"><span data-stu-id="d2524-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="d2524-609">`EventId`0 olarak ayarlanırsa tüm olayları günlüğe göndereceğiz.</span><span class="sxs-lookup"><span data-stu-id="d2524-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="d2524-610">Özel günlükçü oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-610">Create the custom logger</span></span>

<span data-ttu-id="d2524-611">`ILogger`Uygulama kategorisi adı genellikle günlük kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="d2524-612">Örneğin, günlükçü 'nin oluşturulduğu tür:</span><span class="sxs-lookup"><span data-stu-id="d2524-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="d2524-613">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d2524-613">The preceding code:</span></span>

* <span data-ttu-id="d2524-614">Kategori adı başına bir günlükçü örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d2524-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="d2524-615">İade `logLevel == _config.LogLevel` eder `IsEnabled` , her birinin `logLevel` benzersiz bir günlükçü vardır.</span><span class="sxs-lookup"><span data-stu-id="d2524-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="d2524-616">Günlükçüler genellikle daha yüksek günlük seviyeleri için de etkinleştirilmelidir:</span><span class="sxs-lookup"><span data-stu-id="d2524-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="d2524-617">Özel LoggerProvider oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="d2524-618">, `LoggerProvider` Günlükçü örneklerini oluşturan sınıftır.</span><span class="sxs-lookup"><span data-stu-id="d2524-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="d2524-619">Kategori başına bir günlükçü örneği oluşturmak gerekli değildir, ancak bu, NLog veya Log4net gibi bazı Günlükçüler için mantıklı olur.</span><span class="sxs-lookup"><span data-stu-id="d2524-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="d2524-620">Bunu yaptığınızda, gerekirse kategori başına farklı günlüğe kaydetme çıkış hedeflerini de seçebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d2524-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="d2524-621">Yukarıdaki kodda, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> Kategori başına adının tek bir örneğini oluşturur `ColorConsoleLogger` ve içinde depolar [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) .</span><span class="sxs-lookup"><span data-stu-id="d2524-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="d2524-622">Özel günlükçü kullanımı ve kaydı</span><span class="sxs-lookup"><span data-stu-id="d2524-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="d2524-623">Günlükçü kayıt defteri `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d2524-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="d2524-624">Yukarıdaki kod için, için en az bir genişletme yöntemi sağlayın `ILoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="d2524-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="d2524-625">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d2524-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="d2524-626">Günlüğe kaydetme hataları [GitHub.com/DotNet/Runtime/](https://github.com/dotnet/runtime/issues) deposu 'nda oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d2524-627">[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="d2524-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d2524-628">.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="d2524-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d2524-629">Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d2524-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="d2524-630">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2524-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="d2524-631">Sağlayıcı Ekle</span><span class="sxs-lookup"><span data-stu-id="d2524-631">Add providers</span></span>

<span data-ttu-id="d2524-632">Günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="d2524-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="d2524-633">Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar.</span><span class="sxs-lookup"><span data-stu-id="d2524-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="d2524-634">Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="d2524-635">Bir sağlayıcı eklemek için sağlayıcının `Add{provider name}` uzantı yöntemini *program.cs*içinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="d2524-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="d2524-636">Yukarıdaki kod, ve için başvuruları `Microsoft.Extensions.Logging` gerektirir `Microsoft.Extensions.Configuration` .</span><span class="sxs-lookup"><span data-stu-id="d2524-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="d2524-637">Varsayılan proje şablonu, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> aşağıdaki günlük sağlayıcılarını ekleyen çağırır:</span><span class="sxs-lookup"><span data-stu-id="d2524-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="d2524-638">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-638">Console</span></span>
* <span data-ttu-id="d2524-639">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-639">Debug</span></span>
* <span data-ttu-id="d2524-640">EventSource (ASP.NET Core 2,2 ' den başlayarak)</span><span class="sxs-lookup"><span data-stu-id="d2524-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="d2524-641">Kullanıyorsanız `CreateDefaultBuilder` , varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d2524-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="d2524-642"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>Öğesini çağırın ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="d2524-643">Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="d2524-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="d2524-644">Günlükleri oluştur</span><span class="sxs-lookup"><span data-stu-id="d2524-644">Create logs</span></span>

<span data-ttu-id="d2524-645">Günlükler oluşturmak için bir nesnesi kullanın <xref:Microsoft.Extensions.Logging.ILogger%601> .</span><span class="sxs-lookup"><span data-stu-id="d2524-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="d2524-646">Bir Web uygulamasında veya barındırılan hizmette, bir `ILogger` bağımlılık ekleme (dı) kaynağından yararlanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="d2524-647">Konak dışı konsol uygulamalarında, oluşturmak için öğesini kullanın `LoggerFactory` `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="d2524-648">Aşağıdaki ASP.NET Core örnek, kategorisi olarak içeren bir günlükçü oluşturur `TodoApiSample.Pages.AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="d2524-649">Günlük *kategorisi* , her günlük ile ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="d2524-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="d2524-650">`ILogger<T>`Dı tarafından belirtilen örnek, kategori olarak tam nitelikli adı olan Günlükler oluşturur `T` .</span><span class="sxs-lookup"><span data-stu-id="d2524-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="d2524-651">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi olan Günlükler oluşturmak için kullanılır `Information` .</span><span class="sxs-lookup"><span data-stu-id="d2524-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="d2524-652">Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="d2524-653">[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d2524-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="d2524-654">Başlangıçta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-654">Create logs in Startup</span></span>

<span data-ttu-id="d2524-655">Sınıf içindeki günlükleri yazmak için `Startup` , `ILogger` Oluşturucu imzasına bir parametre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d2524-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="d2524-656">Program sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-656">Create logs in the Program class</span></span>

<span data-ttu-id="d2524-657">Sınıf içindeki günlükleri yazmak için `Program` , `ILogger` dı 'den bir örnek alın:</span><span class="sxs-lookup"><span data-stu-id="d2524-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="d2524-658">Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d2524-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="d2524-659">Ancak, ayrı bir günlükçü kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-659">However, a separate logger can be used.</span></span> <span data-ttu-id="d2524-660">Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d2524-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="d2524-661">`AddSerilog`, içinde belirtilen statik yapılandırmayı kullanır `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="d2524-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="d2524-662">Zaman uyumsuz günlükçü yöntemi yok</span><span class="sxs-lookup"><span data-stu-id="d2524-662">No asynchronous logger methods</span></span>

<span data-ttu-id="d2524-663">Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d2524-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="d2524-664">Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="d2524-665">Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="d2524-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="d2524-666">Örneğin, SQL Server için oturum açıyorsanız `Log` Yöntemler zaman uyumlu olduğundan, bunu doğrudan bir yöntemde yapmak istemezsiniz `Log` .</span><span class="sxs-lookup"><span data-stu-id="d2524-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="d2524-667">Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin.</span><span class="sxs-lookup"><span data-stu-id="d2524-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="d2524-668">Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.</span><span class="sxs-lookup"><span data-stu-id="d2524-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="d2524-669">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d2524-669">Configuration</span></span>

<span data-ttu-id="d2524-670">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d2524-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="d2524-671">Dosya biçimleri (ıNı, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="d2524-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="d2524-672">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d2524-672">Command-line arguments.</span></span>
* <span data-ttu-id="d2524-673">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d2524-673">Environment variables.</span></span>
* <span data-ttu-id="d2524-674">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="d2524-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="d2524-675">Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.</span><span class="sxs-lookup"><span data-stu-id="d2524-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="d2524-676">[Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="d2524-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="d2524-677">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="d2524-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="d2524-678">Örneğin, günlük yapılandırma genellikle `Logging` uygulama ayarları dosyalarının bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="d2524-679">Aşağıdaki örnek, dosyasındaki tipik bir *appsettings.Development.js* içeriğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="d2524-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="d2524-680">`Logging`Özelliği, `LogLevel` ve günlük sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="d2524-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="d2524-681">`LogLevel`Altındaki özelliği, `Logging` Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="d2524-682">Örnekte `System` ve `Microsoft` Kategoriler düzeyinde günlüğe kaydedilir `Information` ve diğer tüm diğerleri düzeyinde günlüğe kaydedilir `Debug` .</span><span class="sxs-lookup"><span data-stu-id="d2524-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="d2524-683">`Logging`Günlük sağlayıcılarını belirt altındaki diğer özellikler.</span><span class="sxs-lookup"><span data-stu-id="d2524-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="d2524-684">Örnek, konsol sağlayıcısına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="d2524-684">The example is for the Console provider.</span></span> <span data-ttu-id="d2524-685">Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa, etkinleştirilip etkinleştirilmeyeceğini `IncludeScopes` belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="d2524-686">Bir sağlayıcı özelliği (örnekte olduğu gibi `Console` ), bir özellik de belirtebilir `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="d2524-687">`LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="d2524-688">' De düzeyler belirtilirse `Logging.{providername}.LogLevel` , içinde ayarlanan her şeyi geçersiz kılar `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="d2524-689">Örneğin, aşağıdaki JSON 'u göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d2524-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="d2524-690">Önceki JSON 'da, `Console` sağlayıcı ayarları önceki (varsayılan) günlük düzeyini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d2524-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="d2524-691">Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez.</span><span class="sxs-lookup"><span data-stu-id="d2524-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="d2524-692">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="d2524-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="d2524-693">Örneğin, ayar dosyalarını okumak için tarafından eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) `CreateDefaultBuilder` , günlük yapılandırmasını varsayılan olarak yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="d2524-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="d2524-694">Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="d2524-695">Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="d2524-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="d2524-696">Örnek günlüğe kaydetme çıkışı</span><span class="sxs-lookup"><span data-stu-id="d2524-696">Sample logging output</span></span>

<span data-ttu-id="d2524-697">Yukarıdaki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında Günlükler konsolunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d2524-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="d2524-698">Konsol çıkışının bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d2524-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="d2524-699">Yukarıdaki Günlükler, üzerinde örnek uygulamaya bir HTTP GET isteği yapılarak oluşturulmuştur `http://localhost:5000/api/todo/0` .</span><span class="sxs-lookup"><span data-stu-id="d2524-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="d2524-700">Visual Studio 'da örnek uygulamayı çalıştırdığınızda hata ayıklama penceresinde göründükleri günlüklere yönelik bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d2524-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="d2524-701">`ILogger`Yukarıdaki bölümde gösterilen çağrılar tarafından oluşturulan Günlükler "TodoApi" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="d2524-702">"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır.</span><span class="sxs-lookup"><span data-stu-id="d2524-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="d2524-703">ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="d2524-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="d2524-704">Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d2524-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="d2524-705">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="d2524-705">NuGet packages</span></span>

<span data-ttu-id="d2524-706">`ILogger`Ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde ve bu uygulamalar için varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="d2524-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="d2524-707">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="d2524-707">Log category</span></span>

<span data-ttu-id="d2524-708">Bir `ILogger` nesne oluşturulduğunda, için bir *Kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="d2524-709">Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="d2524-710">Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d2524-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="d2524-711">`ILogger<T>` `ILogger` Kategori olarak tam nitelikli tür adını kullanan bir örnek almak için kullanın `T` :</span><span class="sxs-lookup"><span data-stu-id="d2524-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="d2524-712">Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="d2524-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="d2524-713">`ILogger<T>`, `CreateLogger` tam nitelikli tür adı ile çağırma ile eşdeğerdir `T` .</span><span class="sxs-lookup"><span data-stu-id="d2524-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="d2524-714">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="d2524-714">Log level</span></span>

<span data-ttu-id="d2524-715">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="d2524-716">Günlük düzeyi önem derecesini veya önemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="d2524-717">Örneğin, bir yöntem `Information` normal olarak sona erdiğinde ve bir `Warning` Yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir günlük yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d2524-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="d2524-718">Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:</span><span class="sxs-lookup"><span data-stu-id="d2524-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d2524-719">Yukarıdaki kodda, `MyLogEvents.GetItem` ve `MyLogEvents.GetItemNotFound` parametreleri [günlük olay kimliğidir](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="d2524-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="d2524-720">İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="d2524-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="d2524-721">Yöntem parametreleri bu makaledeki [günlük iletisi şablonu bölümünde](#lmt) açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d2524-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="d2524-722">Yöntem adında düzeyi (örneğin, ve) içeren günlük yöntemleri, `LogInformation` `LogWarning` [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="d2524-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="d2524-723">Bu yöntemler bir `Log` parametre alan bir yöntemi çağırır `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="d2524-724">`Log`Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak söz dizimi görece karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="d2524-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="d2524-725">Daha fazla bilgi için bkz <xref:Microsoft.Extensions.Logging.ILogger> . ve [günlükçü uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="d2524-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="d2524-726">ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="d2524-727">İzleme = 0</span><span class="sxs-lookup"><span data-stu-id="d2524-727">Trace = 0</span></span>

  <span data-ttu-id="d2524-728">Genellikle yalnızca hata ayıklama için değerli bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="d2524-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="d2524-729">Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="d2524-730">*Varsayılan olarak devre dışıdır.*</span><span class="sxs-lookup"><span data-stu-id="d2524-730">*Disabled by default.*</span></span>

* <span data-ttu-id="d2524-731">Hata Ayıkla = 1</span><span class="sxs-lookup"><span data-stu-id="d2524-731">Debug = 1</span></span>

  <span data-ttu-id="d2524-732">Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="d2524-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="d2524-733">Örnek: `Entering method Configure with flag set to true.` `Debug` yüksek günlük hacimden dolayı yalnızca sorun giderirken üretim ortamında düzey günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d2524-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="d2524-734">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="d2524-734">Information = 2</span></span>

  <span data-ttu-id="d2524-735">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="d2524-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="d2524-736">Bu günlüklerde genellikle uzun süreli bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="d2524-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="d2524-737">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="d2524-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="d2524-738">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="d2524-738">Warning = 3</span></span>

  <span data-ttu-id="d2524-739">Uygulama akışında anormal veya beklenmedik olaylar için.</span><span class="sxs-lookup"><span data-stu-id="d2524-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="d2524-740">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="d2524-741">İşlenmiş özel durumlar, günlük düzeyini kullanmak için yaygın bir yerdir `Warning` .</span><span class="sxs-lookup"><span data-stu-id="d2524-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="d2524-742">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="d2524-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="d2524-743">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="d2524-743">Error = 4</span></span>

  <span data-ttu-id="d2524-744">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="d2524-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="d2524-745">Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="d2524-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="d2524-746">Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="d2524-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="d2524-747">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="d2524-747">Critical = 5</span></span>

  <span data-ttu-id="d2524-748">Anında ilgilenilmesi gereken hatalarda.</span><span class="sxs-lookup"><span data-stu-id="d2524-748">For failures that require immediate attention.</span></span> <span data-ttu-id="d2524-749">Örnekler: veri kaybı senaryoları, disk alanı yetersiz.</span><span class="sxs-lookup"><span data-stu-id="d2524-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="d2524-750">Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="d2524-751">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d2524-751">For example:</span></span>

* <span data-ttu-id="d2524-752">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="d2524-752">In production:</span></span>
  * <span data-ttu-id="d2524-753">Geçiş düzeyinde günlüğe kaydetme, `Trace` `Information` yüksek hacimli ayrıntılı günlük iletileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d2524-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="d2524-754">Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, `Trace` `Information` düzey iletiler üzerinden yüksek hacimli, düşük maliyetli bir veri deposuna oturum açın.</span><span class="sxs-lookup"><span data-stu-id="d2524-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="d2524-755">Düzeylerde oturum açma işlemi `Warning` `Critical` genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="d2524-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="d2524-756">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.</span><span class="sxs-lookup"><span data-stu-id="d2524-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="d2524-757">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="d2524-757">During development:</span></span>
  * <span data-ttu-id="d2524-758">`Warning`Konsola iletiler aracılığıyla oturum açın `Critical` .</span><span class="sxs-lookup"><span data-stu-id="d2524-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="d2524-759">`Trace` `Information` Sorun giderirken iletiler aracılığıyla ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="d2524-760">Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d2524-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="d2524-761">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="d2524-762">Bu makalenin önceki kısımlarında yer alınan günlük örnekleri, günlüklerin altında tutulur `Information` , bu nedenle hiçbir `Debug` veya `Trace` düzeyi günlük oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d2524-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="d2524-763">Günlükleri göstermek için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği aşağıda verilmiştir `Debug` :</span><span class="sxs-lookup"><span data-stu-id="d2524-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="d2524-764">Günlüğe olay KIMLIĞI</span><span class="sxs-lookup"><span data-stu-id="d2524-764">Log event ID</span></span>

<span data-ttu-id="d2524-765">Her günlük bir *olay kimliği*belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="d2524-766">Örnek uygulama bunu yerel olarak tanımlanmış bir sınıf kullanarak yapar `LoggingEvents` :</span><span class="sxs-lookup"><span data-stu-id="d2524-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="d2524-767">Olay KIMLIĞI bir olay kümesini ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="d2524-767">An event ID associates a set of events.</span></span> <span data-ttu-id="d2524-768">Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="d2524-769">Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="d2524-770">Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor.</span><span class="sxs-lookup"><span data-stu-id="d2524-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="d2524-771">Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:</span><span class="sxs-lookup"><span data-stu-id="d2524-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="d2524-772">Günlük iletisi şablonu</span><span class="sxs-lookup"><span data-stu-id="d2524-772">Log message template</span></span>

<span data-ttu-id="d2524-773">Her günlük bir ileti şablonunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-773">Each log specifies a message template.</span></span> <span data-ttu-id="d2524-774">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="d2524-775">Sayılar değil, yer tutucular için adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d2524-776">Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası.</span><span class="sxs-lookup"><span data-stu-id="d2524-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="d2524-777">Aşağıdaki kodda, parametre adlarının ileti şablonunda sıra dışı olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="d2524-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="d2524-778">Bu kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d2524-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="d2524-779">Günlüğe kaydetme altyapısı bu şekilde çalışarak, günlük sağlayıcılarının [yapılandırılmış günlüğe yazma olarak da bilinen anlamsal günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulayabilmesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="d2524-780">Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="d2524-781">Bu bilgiler, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="d2524-782">Örneğin, günlükçü yönteminin şuna benzer şekilde göründüğünü varsayın:</span><span class="sxs-lookup"><span data-stu-id="d2524-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="d2524-783">Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı, `ID` `RequestTime` günlük verilerinde sorguları basitleştiren ve özelliklere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="d2524-784">Bir sorgu, belirli bir aralıktaki tüm günlükleri, `RequestTime` Kısa mesajdan zaman aşımını ayrıştırmadan bulabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="d2524-785">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="d2524-785">Logging exceptions</span></span>

<span data-ttu-id="d2524-786">Günlükçü yöntemlerinin, aşağıdaki örnekte olduğu gibi bir özel durum iletmenizi sağlayan aşırı yüklemeleri vardır:</span><span class="sxs-lookup"><span data-stu-id="d2524-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="d2524-787">Özel durum bilgileri farklı yollarla farklı sağlayıcılarda işler.</span><span class="sxs-lookup"><span data-stu-id="d2524-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="d2524-788">Yukarıda gösterilen koddan hata ayıklama sağlayıcısı çıktısına bir örnek aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="d2524-789">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="d2524-789">Log filtering</span></span>

<span data-ttu-id="d2524-790">Belirli bir sağlayıcı ve kategori için en az bir günlük düzeyi veya tüm sağlayıcılar ya da tüm kategoriler için belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d2524-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="d2524-791">Minimum düzeyin altındaki tüm Günlükler bu sağlayıcıya aktarılmaz, bu nedenle görüntülenmez veya depolanmaz.</span><span class="sxs-lookup"><span data-stu-id="d2524-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="d2524-792">Tüm günlükleri gizlemek için `LogLevel.None` En düşük günlük düzeyi olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="d2524-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="d2524-793">Öğesinin tamsayı değeri `LogLevel.None` 6 ' dır `LogLevel.Critical` (5) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="d2524-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="d2524-794">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="d2524-794">Create filter rules in configuration</span></span>

<span data-ttu-id="d2524-795">Proje şablonu kodu, `CreateDefaultBuilder` konsol, hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="d2524-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="d2524-796">`CreateDefaultBuilder`Yöntemi `Logging` , [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, bir bölümde yapılandırmayı aramak için günlük kaydı yapar.</span><span class="sxs-lookup"><span data-stu-id="d2524-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="d2524-797">Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="d2524-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="d2524-798">Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="d2524-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="d2524-799">Bir nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="d2524-800">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="d2524-800">Filter rules in code</span></span>

<span data-ttu-id="d2524-801">Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="d2524-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="d2524-802">İkincisi, `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="d2524-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="d2524-803">Birincisi `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="d2524-804">Filtreleme kuralları nasıl uygulanır</span><span class="sxs-lookup"><span data-stu-id="d2524-804">How filtering rules are applied</span></span>

<span data-ttu-id="d2524-805">Yapılandırma verileri ve `AddFilter` Önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d2524-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="d2524-806">İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.</span><span class="sxs-lookup"><span data-stu-id="d2524-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="d2524-807">Sayı</span><span class="sxs-lookup"><span data-stu-id="d2524-807">Number</span></span> | <span data-ttu-id="d2524-808">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d2524-808">Provider</span></span>      | <span data-ttu-id="d2524-809">Şununla başlayan Kategoriler...</span><span class="sxs-lookup"><span data-stu-id="d2524-809">Categories that begin with ...</span></span>          | <span data-ttu-id="d2524-810">En düşük günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="d2524-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="d2524-811">1</span><span class="sxs-lookup"><span data-stu-id="d2524-811">1</span></span>      | <span data-ttu-id="d2524-812">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-812">Debug</span></span>         | <span data-ttu-id="d2524-813">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="d2524-813">All categories</span></span>                          | <span data-ttu-id="d2524-814">Bilgi</span><span class="sxs-lookup"><span data-stu-id="d2524-814">Information</span></span>       |
| <span data-ttu-id="d2524-815">2</span><span class="sxs-lookup"><span data-stu-id="d2524-815">2</span></span>      | <span data-ttu-id="d2524-816">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-816">Console</span></span>       | <span data-ttu-id="d2524-817">Microsoft. AspNetCore. Mvc. Razor . İç</span><span class="sxs-lookup"><span data-stu-id="d2524-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="d2524-818">Uyarı</span><span class="sxs-lookup"><span data-stu-id="d2524-818">Warning</span></span>           |
| <span data-ttu-id="d2524-819">3</span><span class="sxs-lookup"><span data-stu-id="d2524-819">3</span></span>      | <span data-ttu-id="d2524-820">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-820">Console</span></span>       | <span data-ttu-id="d2524-821">Microsoft. AspNetCore. Mvc. Razor .Razor</span><span class="sxs-lookup"><span data-stu-id="d2524-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="d2524-822">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-822">Debug</span></span>             |
| <span data-ttu-id="d2524-823">4</span><span class="sxs-lookup"><span data-stu-id="d2524-823">4</span></span>      | <span data-ttu-id="d2524-824">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-824">Console</span></span>       | <span data-ttu-id="d2524-825">Microsoft. AspNetCore. Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="d2524-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="d2524-826">Hata</span><span class="sxs-lookup"><span data-stu-id="d2524-826">Error</span></span>             |
| <span data-ttu-id="d2524-827">5</span><span class="sxs-lookup"><span data-stu-id="d2524-827">5</span></span>      | <span data-ttu-id="d2524-828">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-828">Console</span></span>       | <span data-ttu-id="d2524-829">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="d2524-829">All categories</span></span>                          | <span data-ttu-id="d2524-830">Bilgi</span><span class="sxs-lookup"><span data-stu-id="d2524-830">Information</span></span>       |
| <span data-ttu-id="d2524-831">6</span><span class="sxs-lookup"><span data-stu-id="d2524-831">6</span></span>      | <span data-ttu-id="d2524-832">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="d2524-832">All providers</span></span> | <span data-ttu-id="d2524-833">Tüm kategoriler</span><span class="sxs-lookup"><span data-stu-id="d2524-833">All categories</span></span>                          | <span data-ttu-id="d2524-834">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-834">Debug</span></span>             |
| <span data-ttu-id="d2524-835">7</span><span class="sxs-lookup"><span data-stu-id="d2524-835">7</span></span>      | <span data-ttu-id="d2524-836">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="d2524-836">All providers</span></span> | <span data-ttu-id="d2524-837">Sistem</span><span class="sxs-lookup"><span data-stu-id="d2524-837">System</span></span>                                  | <span data-ttu-id="d2524-838">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-838">Debug</span></span>             |
| <span data-ttu-id="d2524-839">8</span><span class="sxs-lookup"><span data-stu-id="d2524-839">8</span></span>      | <span data-ttu-id="d2524-840">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-840">Debug</span></span>         | <span data-ttu-id="d2524-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="d2524-841">Microsoft</span></span>                               | <span data-ttu-id="d2524-842">İzleme</span><span class="sxs-lookup"><span data-stu-id="d2524-842">Trace</span></span>             |

<span data-ttu-id="d2524-843">Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="d2524-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="d2524-844">Bir örnek tarafından yazılan tüm iletiler `ILogger` , seçilen kurallara göre filtrelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="d2524-845">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="d2524-846">Belirli bir kategori için oluşturulan her sağlayıcı için aşağıdaki algoritma kullanılır `ILogger` :</span><span class="sxs-lookup"><span data-stu-id="d2524-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="d2524-847">Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="d2524-848">Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="d2524-849">Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="d2524-850">Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="d2524-851">Birden çok kural seçilirse, **son** olanı götürün.</span><span class="sxs-lookup"><span data-stu-id="d2524-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="d2524-852">Hiçbir kural seçilmezse, kullanın `MinimumLevel` .</span><span class="sxs-lookup"><span data-stu-id="d2524-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="d2524-853">Yukarıdaki kurallarla ilgili olarak, `ILogger` "Microsoft. AspNetCore. Mvc." adlı kategori için bir nesne oluşturduğunuzu varsayalım. Razor Razor ViewEngine ":</span><span class="sxs-lookup"><span data-stu-id="d2524-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="d2524-854">Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="d2524-855">Kural 8 ' i en özeldir, yani seçili olanı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="d2524-856">Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="d2524-857">Kural 3 en özeldir.</span><span class="sxs-lookup"><span data-stu-id="d2524-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="d2524-858">Elde edilen `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzeyi ve üzeri Günlükler gönderir.</span><span class="sxs-lookup"><span data-stu-id="d2524-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="d2524-859">`Debug`Düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="d2524-860">Sağlayıcı diğer adları</span><span class="sxs-lookup"><span data-stu-id="d2524-860">Provider aliases</span></span>

<span data-ttu-id="d2524-861">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="d2524-862">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="d2524-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="d2524-863">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-863">Console</span></span>
* <span data-ttu-id="d2524-864">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-864">Debug</span></span>
* <span data-ttu-id="d2524-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="d2524-865">EventSource</span></span>
* <span data-ttu-id="d2524-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="d2524-866">EventLog</span></span>
* <span data-ttu-id="d2524-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d2524-867">TraceSource</span></span>
* <span data-ttu-id="d2524-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d2524-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="d2524-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d2524-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="d2524-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d2524-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="d2524-871">Varsayılan en düşük düzey</span><span class="sxs-lookup"><span data-stu-id="d2524-871">Default minimum level</span></span>

<span data-ttu-id="d2524-872">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kural uygulanmaz geçerli olan en düşük düzey ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="d2524-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="d2524-873">Aşağıdaki örnekte, en düşük düzeyin nasıl ayarlanacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="d2524-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="d2524-874">En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer olur `Information` ve bu, `Trace` `Debug` günlüklerin yok sayılacağı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d2524-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="d2524-875">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="d2524-875">Filter functions</span></span>

<span data-ttu-id="d2524-876">Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="d2524-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="d2524-877">İşlevindeki kodun sağlayıcı türü, kategorisi ve günlük düzeyine erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="d2524-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="d2524-878">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d2524-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="d2524-879">Sistem kategorileri ve Düzeyler</span><span class="sxs-lookup"><span data-stu-id="d2524-879">System categories and levels</span></span>

<span data-ttu-id="d2524-880">ASP.NET Core ve Entity Framework Core tarafından kullanılan bazı kategoriler şunlardır ve bunlardan beklenen Günlükler hakkında notlar bulunur:</span><span class="sxs-lookup"><span data-stu-id="d2524-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="d2524-881">Kategori</span><span class="sxs-lookup"><span data-stu-id="d2524-881">Category</span></span>                            | <span data-ttu-id="d2524-882">Notlar</span><span class="sxs-lookup"><span data-stu-id="d2524-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="d2524-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="d2524-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="d2524-884">Genel ASP.NET Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="d2524-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="d2524-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="d2524-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="d2524-886">Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı.</span><span class="sxs-lookup"><span data-stu-id="d2524-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="d2524-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="d2524-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="d2524-888">İzin verilen konaklar.</span><span class="sxs-lookup"><span data-stu-id="d2524-888">Hosts allowed.</span></span> |
| <span data-ttu-id="d2524-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="d2524-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="d2524-890">HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları.</span><span class="sxs-lookup"><span data-stu-id="d2524-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="d2524-891">Hangi barındırma başlangıç derlemeleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="d2524-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="d2524-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="d2524-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="d2524-893">MVC ve Razor Tanılama.</span><span class="sxs-lookup"><span data-stu-id="d2524-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="d2524-894">Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="d2524-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="d2524-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="d2524-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="d2524-896">Eşleşen bilgileri yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="d2524-896">Route matching information.</span></span> |
| <span data-ttu-id="d2524-897">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="d2524-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="d2524-898">Bağlantı başlatın, durdurun ve canlı yanıtları koruyun.</span><span class="sxs-lookup"><span data-stu-id="d2524-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="d2524-899">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="d2524-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="d2524-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="d2524-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="d2524-901">Sunulan dosyalar.</span><span class="sxs-lookup"><span data-stu-id="d2524-901">Files served.</span></span> |
| <span data-ttu-id="d2524-902">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d2524-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="d2524-903">Genel Entity Framework Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="d2524-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="d2524-904">Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="d2524-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="d2524-905">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="d2524-905">Log scopes</span></span>

 <span data-ttu-id="d2524-906">*Kapsam* bir mantıksal işlemler kümesini gruplandırabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="d2524-907">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="d2524-908">Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="d2524-909">Kapsam `IDisposable` , yöntemi tarafından döndürülen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> ve atılana kadar sürer olan bir türdür.</span><span class="sxs-lookup"><span data-stu-id="d2524-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="d2524-910">Bir blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın `using` :</span><span class="sxs-lookup"><span data-stu-id="d2524-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="d2524-911">Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="d2524-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="d2524-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2524-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="d2524-913">`IncludeScopes`Kapsam tabanlı günlüğe kaydetmeyi etkinleştirmek için konsol günlükçü seçeneğinin yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d2524-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="d2524-914">Yapılandırma hakkında bilgi için [yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d2524-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="d2524-915">Her günlük iletisi kapsamlı bilgiler içerir:</span><span class="sxs-lookup"><span data-stu-id="d2524-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="d2524-916">Yerleşik günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-916">Built-in logging providers</span></span>

<span data-ttu-id="d2524-917">ASP.NET Core aşağıdaki sağlayıcıları sevk eder:</span><span class="sxs-lookup"><span data-stu-id="d2524-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="d2524-918">Konsol</span><span class="sxs-lookup"><span data-stu-id="d2524-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="d2524-919">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d2524-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="d2524-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="d2524-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="d2524-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="d2524-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="d2524-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d2524-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="d2524-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d2524-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d2524-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d2524-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d2524-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d2524-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="d2524-926">Stdout ve ASP.NET Core modüllü hata ayıklama günlüğü hakkında bilgi için bkz <xref:test/troubleshoot-azure-iis> . ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="d2524-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="d2524-927">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d2524-927">Console provider</span></span>

<span data-ttu-id="d2524-928">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi, günlük çıktısını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="d2524-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="d2524-929">Konsol günlüğü çıkışını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d2524-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="d2524-930">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d2524-930">Debug provider</span></span>

<span data-ttu-id="d2524-931">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (Yöntem çağrıları) kullanarak günlük çıktısını yazar `Debug.WriteLine` .</span><span class="sxs-lookup"><span data-stu-id="d2524-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="d2524-932">Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="d2524-933">Olay kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d2524-933">Event Source provider</span></span>

<span data-ttu-id="d2524-934">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, adıyla bir olay kaynağı çapraz platformuna yazar `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="d2524-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="d2524-935">Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="d2524-936">Olay kaynak sağlayıcısı, `CreateDefaultBuilder` konağı oluşturmak için çağrıldığında otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="d2524-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="d2524-937">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="d2524-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="d2524-938">ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="d2524-939">Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="d2524-940">(Dizenin başlangıcında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="d2524-940">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="d2524-942">Windows olay günlüğü sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d2524-942">Windows EventLog provider</span></span>

<span data-ttu-id="d2524-943">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.</span><span class="sxs-lookup"><span data-stu-id="d2524-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="d2524-944">[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) , geçiş yapmanızı sağlar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="d2524-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="d2524-945">`null`Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d2524-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="d2524-946">`LogName`: "Uygulama"</span><span class="sxs-lookup"><span data-stu-id="d2524-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="d2524-947">`SourceName`: ".NET çalışma zamanı"</span><span class="sxs-lookup"><span data-stu-id="d2524-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="d2524-948">`MachineName`: Yerel makine adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d2524-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="d2524-949">Olaylar, [Uyarı düzeyi ve üzeri](#log-level)için günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="d2524-950">Aşağıdaki örnek, olay günlüğü varsayılan günlük düzeyini şu şekilde ayarlar <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d2524-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="d2524-951">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d2524-951">TraceSource provider</span></span>

<span data-ttu-id="d2524-952">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d2524-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="d2524-953">[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d2524-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="d2524-954">Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine).</span><span class="sxs-lookup"><span data-stu-id="d2524-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="d2524-955">Sağlayıcı, iletileri örnek uygulamada kullanılan gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir <xref:System.Diagnostics.TextWriterTraceListener> .</span><span class="sxs-lookup"><span data-stu-id="d2524-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="d2524-956">Azure App Service sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d2524-956">Azure App Service provider</span></span>

<span data-ttu-id="d2524-957">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="d2524-958">Sağlayıcı paketi [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="d2524-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d2524-959">.NET Framework veya `Microsoft.AspNetCore.App` metapackage 'e başvuru yaparken, sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="d2524-960"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>Aşırı yükleme, geçiş yapmanızı sağlar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> .</span><span class="sxs-lookup"><span data-stu-id="d2524-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="d2524-961">Ayarlar nesnesi, günlük çıkış şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="d2524-962">(*Çıktı şablonu* , bir yöntem çağrısıyla birlikte sağlandığının yanı sıra tüm günlüklere uygulanan bir ileti şablonudur `ILogger` .)</span><span class="sxs-lookup"><span data-stu-id="d2524-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="d2524-963">App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d2524-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="d2524-964">Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="d2524-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="d2524-965">**Uygulama günlüğü (dosya sistemi)**</span><span class="sxs-lookup"><span data-stu-id="d2524-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="d2524-966">**Uygulama günlüğü (blob)**</span><span class="sxs-lookup"><span data-stu-id="d2524-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="d2524-967">Günlük dosyaları için varsayılan konum *D: \\ Home \\ LogFiles \\ uygulama* klasöründedir ve varsayılan dosya adı *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="d2524-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="d2524-968">Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d2524-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="d2524-969">Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid} -applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="d2524-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="d2524-970">Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="d2524-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="d2524-971">Proje yerel olarak çalıştırıldığında, &mdash; yerel dosyalara veya blob 'lar için yerel geliştirme depolamasına yazmazsa, bu, hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="d2524-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="d2524-972">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="d2524-972">Azure log streaming</span></span>

<span data-ttu-id="d2524-973">Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:</span><span class="sxs-lookup"><span data-stu-id="d2524-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="d2524-974">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="d2524-974">The app server</span></span>
* <span data-ttu-id="d2524-975">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="d2524-975">The web server</span></span>
* <span data-ttu-id="d2524-976">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="d2524-976">Failed request tracing</span></span>

<span data-ttu-id="d2524-977">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="d2524-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="d2524-978">Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d2524-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="d2524-979">**Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="d2524-980">Günlük **düzeyini**seçin.</span><span class="sxs-lookup"><span data-stu-id="d2524-980">Choose the log **Level**.</span></span> <span data-ttu-id="d2524-981">Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d2524-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="d2524-982">Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d2524-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="d2524-983">Uygulama tarafından arabirim aracılığıyla günlüğe kaydedilir `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d2524-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="d2524-984">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="d2524-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="d2524-985">[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar.</span><span class="sxs-lookup"><span data-stu-id="d2524-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="d2524-986">Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="d2524-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="d2524-987">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d2524-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="d2524-988">Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d2524-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="d2524-989">Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.</span><span class="sxs-lookup"><span data-stu-id="d2524-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="d2524-990">ASP.NET 4. x için [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini kullanmayın &mdash; .</span><span class="sxs-lookup"><span data-stu-id="d2524-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="d2524-991">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="d2524-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="d2524-992">Application Insights'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="d2524-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="d2524-993">[ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="d2524-994">[.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="d2524-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="d2524-995">[Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="d2524-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="d2524-996">Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .</span><span class="sxs-lookup"><span data-stu-id="d2524-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="d2524-997">Üçüncü taraf günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d2524-997">Third-party logging providers</span></span>

<span data-ttu-id="d2524-998">ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="d2524-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="d2524-999">[ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="d2524-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="d2524-1001">[Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="d2524-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="d2524-1002">[KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="d2524-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="d2524-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="d2524-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="d2524-1004">[Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="d2524-1005">[NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d2524-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="d2524-1006">[Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d2524-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="d2524-1007">[Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="d2524-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="d2524-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d2524-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="d2524-1009">Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="d2524-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d2524-1010">Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="d2524-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="d2524-1011">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d2524-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="d2524-1012">`ILoggerFactory`Günlüğe kaydetme çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="d2524-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="d2524-1013">Daha fazla bilgi için bkz. her sağlayıcının belgeleri.</span><span class="sxs-lookup"><span data-stu-id="d2524-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="d2524-1014">Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d2524-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d2524-1015">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d2524-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
