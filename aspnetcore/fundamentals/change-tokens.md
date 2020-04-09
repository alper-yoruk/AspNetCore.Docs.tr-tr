---
title: ASP.NET Core'daki değişiklik belirteçleriyle değişiklikleri algılama
author: rick-anderson
description: Değişiklikleri izlemek için değişiklik belirteçlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656347"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="1a9f3-103">ASP.NET Core'daki değişiklik belirteçleriyle değişiklikleri algılama</span><span class="sxs-lookup"><span data-stu-id="1a9f3-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a9f3-104">*Değişiklik belirteci,* durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzeyli bir yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="1a9f3-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a9f3-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="1a9f3-106">IChangeToken arabirimi</span><span class="sxs-lookup"><span data-stu-id="1a9f3-106">IChangeToken interface</span></span>

<span data-ttu-id="1a9f3-107"><xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin meydana geldiğini bildirimleri yayır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="1a9f3-108">`IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a9f3-109">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi ASP.NET Core uygulamalarına dolaylı olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="1a9f3-110">`IChangeToken`iki özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="1a9f3-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirteç leri proaktif olarak geri aramaları yükseltip yükseltmeyin.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="1a9f3-112">`ActiveChangedCallbacks` Ayarlanmışsa, geri arama asla çağrılmez ve uygulamanın değişiklikler için anket `HasChanged` yapması `false`gerekir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="1a9f3-113">Bir belirteci hiçbir değişiklik meydana gelmezse veya temel değişiklik dinleyicisi imha edilirse veya devre dışı bırakılırsa asla iptal edilmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="1a9f3-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişiklik gerçekleşip gerçekleşmediğini gösteren bir değer alır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="1a9f3-115">Arabirim, belirteç değiştiğinde çağrılan bir geri aramayı kaydeden [RegisterChangeCallback (Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `IChangeToken`</span><span class="sxs-lookup"><span data-stu-id="1a9f3-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="1a9f3-116">`HasChanged`geri arama çağrılmadan önce ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="1a9f3-117">ChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="1a9f3-117">ChangeToken class</span></span>

<span data-ttu-id="1a9f3-118"><xref:Microsoft.Extensions.Primitives.ChangeToken>bir değişikliğin meydana geldiği bildirimleri yaymak için kullanılan statik bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="1a9f3-119">`ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a9f3-120">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi ASP.NET Core uygulamalarına dolaylı olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="1a9f3-121">[ChangeToken.OnChange(Func\<IChangeToken>, Eylem)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi `Action` belirteç değiştiğinde aramak için bir kayıt:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="1a9f3-122">`Func<IChangeToken>`belirteci üretir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="1a9f3-123">`Action`belirteç değiştiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="1a9f3-124">[ChangeToken.OnChange\<TState\<>(Func IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yük belirteç tüketici `TState` `Action`içine geçti ek bir parametre alır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="1a9f3-125">`OnChange`bir <xref:System.IDisposable>' yi döndürür.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="1a9f3-126">Arama, <xref:System.IDisposable.Dispose*> belirtecinin daha fazla değişiklik için dinlemesini durdurur ve belirteç kaynaklarını serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="1a9f3-127">ASP.NET Core'daki değişiklik belirteçlerinin örnek kullanımları</span><span class="sxs-lookup"><span data-stu-id="1a9f3-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1a9f3-128">Değişiklik belirteçleri, nesnelerdeki değişiklikleri izlemek için ASP.NET Core'un önemli alanlarında kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="1a9f3-129">Dosyalardaki değişiklikleri izlemek <xref:Microsoft.Extensions.FileProviders.IFileProvider>için, <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 'ın `IChangeToken` yöntemi, belirtilen dosyaların veya klasörün izlemesi için bir yöntem oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="1a9f3-130">`IChangeToken`belirteçleri, değişiklik üzerindeki önbellek tahliyelerini tetiklemek için önbellek girişlerine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="1a9f3-131">Değişiklikler `TOptions` için varsayılan <xref:Microsoft.Extensions.Options.OptionsMonitor`1> uygulama, bir veya daha fazla <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> örneği kabul eden aşırı yüklemeye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="1a9f3-132">Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için bir değişiklik bildirimi geri araması kaydetmek için bir geri döndürür.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="1a9f3-133">Yapılandırma değişiklikleri için monitör</span><span class="sxs-lookup"><span data-stu-id="1a9f3-133">Monitor for configuration changes</span></span>

<span data-ttu-id="1a9f3-134">Varsayılan olarak, ASP.NET Core şablonları [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) *(appsettings.json*, *appsettings) kullanır. Development.json*ve *uygulama ayarları. Production.json*) uygulama yapılandırma ayarlarını yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="1a9f3-135">Bu dosyalar [addjsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) uzantı yöntemi <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="1a9f3-136">`reloadOnChange`dosya değişikliklerinde yapılandırmanın yeniden yüklenmesi gerekiyorsa gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="1a9f3-137">Bu ayar <xref:Microsoft.Extensions.Hosting.Host> kolaylık yönteminde <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>görünür:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="1a9f3-138">Dosya tabanlı yapılandırma <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="1a9f3-139">`FileConfigurationSource`dosyaları <xref:Microsoft.Extensions.FileProviders.IFileProvider> izlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="1a9f3-140">Varsayılan olarak, `IFileMonitor` yapılandırma dosyası <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>değişiklikleri <xref:System.IO.FileSystemWatcher> için izlemek için kullanan bir , tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="1a9f3-141">Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="1a9f3-142">Herhangi bir *uygulama ayarları* dosyaları değişirse, dosya izleme uygulamalarının her ikisi de özel kod&mdash;yürütmek örnek uygulama konsola bir mesaj yazar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="1a9f3-143">Yapılandırma dosyası, `FileSystemWatcher` tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri aramasını tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="1a9f3-144">Özel kodun birden çok belirteç geri araması tetiklendiğinde yalnızca bir kez çalıştırıldığından emin olmak için, örneğin uygulama dosyası işlenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="1a9f3-145">Örnek SHA1 dosya karma kullanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="1a9f3-146">Bir yeniden deneme üstel geri leme ile uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="1a9f3-147">Dosya kilitleme, dosyaüzerinde yeni bir karmanın bilgisayara kullanımını geçici olarak engelleyen oluşabilir, çünkü yeniden deneme vardır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="1a9f3-148">*Yardımcı Programlar/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="1a9f3-149">Basit başlangıç değişikliği belirteci</span><span class="sxs-lookup"><span data-stu-id="1a9f3-149">Simple startup change token</span></span>

<span data-ttu-id="1a9f3-150">Yapılandırma yeniden yükleme `Action` belirteci değişiklik bildirimleri için bir belirteç tüketici geri arama kaydedin.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="1a9f3-151">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="1a9f3-152">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="1a9f3-153">Geri arama `InvokeChanged` yöntemidir:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="1a9f3-154">Geri arama, izlemek için doğru `IWebHostEnvironment`uygulama *ayarları* yapılandırma dosyasını (örneğin, uygulama ayarları) belirtmek için yararlı olan , içinde geçmek için kullanılır. `state` \* Development.json\* zaman Geliştirme ortamında).</span><span class="sxs-lookup"><span data-stu-id="1a9f3-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="1a9f3-155">Dosya işlenmeleri, yapılandırma `WriteConsole` dosyası yalnızca bir kez değiştiğinde birden çok belirteç geri araması nedeniyle deyimin birden çok kez çalışmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="1a9f3-156">Bu sistem, uygulama çalıştığı sürece çalışır ve kullanıcı tarafından devre dışı tutulamaz.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="1a9f3-157">Hizmet olarak yapılandırma değişikliklerini izleme</span><span class="sxs-lookup"><span data-stu-id="1a9f3-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="1a9f3-158">Örnek uygular:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-158">The sample implements:</span></span>

* <span data-ttu-id="1a9f3-159">Temel başlangıç belirteç izleme.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="1a9f3-160">Bir hizmet olarak izleme.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-160">Monitoring as a service.</span></span>
* <span data-ttu-id="1a9f3-161">İzlemeyi etkinleştirmek ve devre dışı kılabilir bir mekanizma.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="1a9f3-162">Örnek bir `IConfigurationMonitor` arayüz kurar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="1a9f3-163">*Uzantılar/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="1a9f3-164">Uygulanan sınıfın oluşturucusu, `ConfigurationMonitor`değişiklik bildirimleri için bir geri arama kaydeder:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="1a9f3-165">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="1a9f3-166">`InvokeChanged`geri arama yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="1a9f3-167">Bu `state` örnekte, izleme durumuna `IConfigurationMonitor` erişmek için kullanılan örneğin başvurusudur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="1a9f3-168">İki özellik kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-168">Two properties are used:</span></span>

* <span data-ttu-id="1a9f3-169">`MonitoringEnabled`&ndash; Geri aramanın özel kodunu çalıştırıp çalıştırmaması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-169">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="1a9f3-170">`CurrentState`&ndash; Kullanıcı Arabirimi'nde kullanılmak üzere geçerli izleme durumunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-170">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="1a9f3-171">Yöntem, `InvokeChanged` önceki yaklaşıma benzer, ancak bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="1a9f3-172">Kodu çalışmadığı sürece `MonitoringEnabled` `true`çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="1a9f3-173">`WriteConsole` Çıktısındaki akımı `state` çıkar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="1a9f3-174">Bir `ConfigurationMonitor` örnek bir hizmet `Startup.ConfigureServices`olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1a9f3-175">Dizin sayfası yapılandırma izleme üzerinde kullanıcı denetimi sunar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="1a9f3-176">Örneği içine `IConfigurationMonitor` enjekte `IndexModel`edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="1a9f3-177">*Sayfalar/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1a9f3-178">Yapılandırma monitörü`_monitor`( ) izlemeyi etkinleştirmek veya devre dışı kalmak ve Kullanıcı Arabirimi geri bildirimi için geçerli durumu ayarlamak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1a9f3-179">`OnPostStartMonitoring` Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="1a9f3-180">`OnPostStopMonitoring` Tetiklendiğinde, izleme devre dışı bırakılır ve durum izlemenin oluşmadığını yansıtacak şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="1a9f3-181">Kullanıcı UI'deki düğmeler izlemeyi etkinleştiriyor ve devre dışı bıtır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="1a9f3-182">*Sayfalar/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="1a9f3-183">Önbelleğe alınmış dosya değişikliklerini izleme</span><span class="sxs-lookup"><span data-stu-id="1a9f3-183">Monitor cached file changes</span></span>

<span data-ttu-id="1a9f3-184">Dosya içeriği bellekte önbelleğe <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>alınabilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="1a9f3-185">Bellek içi önbelleğe alma, [Bellek tesisi](xref:performance/caching/memory) başlığında açıklanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="1a9f3-186">Aşağıda açıklanan uygulama gibi ek adımlar atmadan, kaynak veri değişirse *eski* (eski) veriler önbellekten döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="1a9f3-187">Örneğin, [sürgülü](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) bir son kullanma süresini yenilerken önbelleğe alınmış bir kaynak dosyanın durumunu hesaba katmamak, eski önbelleğe alınmış dosya verilerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="1a9f3-188">Veriler için her istek kayan son kullanma süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="1a9f3-189">Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, muhtemelen eski içeriğe tabidir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="1a9f3-190">Dosya önbelleğe alma senaryosunda değişiklik belirteçleri kullanmak önbellekte eski dosya içeriğinin bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="1a9f3-191">Örnek uygulama, yaklaşımın bir uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="1a9f3-192">Örnek için `GetFileContent` kullanır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="1a9f3-193">Dosya içeriğini iade edin.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-193">Return file content.</span></span>
* <span data-ttu-id="1a9f3-194">Dosya kilidinin dosyanın okunmasını geçici olarak engellediği durumları kapsayacak şekilde üstel yedekleme içeren bir yeniden deneme algoritması uygulayın.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="1a9f3-195">*Yardımcı Programlar/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="1a9f3-196">Önbelleğe alınmış dosya aramalarını işlemek için a `FileService` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="1a9f3-197">Hizmetin `GetFileContent` yöntem çağrısı, bellek içi önbellekten dosya içeriğini elde etmeye ve arayana *(Hizmetler/FileService.cs)* döndürmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="1a9f3-198">Önbelleğe alınan içerik önbellek anahtarı kullanılarak bulunmazsa, aşağıdaki işlemler yapılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="1a9f3-199">Dosya içeriği kullanılarak `GetFileContent`elde edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="1a9f3-200">Bir değişiklik belirteci [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile dosya sağlayıcısından elde edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="1a9f3-201">Dosya değiştirildiğinde belirteç geri arama tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="1a9f3-202">Dosya içeriği sürgülü bir [son kullanma](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alındı.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="1a9f3-203">Dosya önbelleğe alınmışken değişirse önbellek girişini çıkarmak için [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte değişiklik belirteci eklenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="1a9f3-204">Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)depolanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="1a9f3-205">`IWebHostEnvironment.ContentRootFileProvider`uygulamanın bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> işaret elde etmek `IWebHostEnvironment.ContentRootPath`için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="1a9f3-206">`filePath` [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="1a9f3-207">Bellek `FileService` önbelleğe alma hizmeti ile birlikte servis konteynerinde kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="1a9f3-208">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="1a9f3-209">Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="1a9f3-210">Dizin sayfasının `OnGet` yönteminde (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="1a9f3-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="1a9f3-211">CompositeChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="1a9f3-211">CompositeChangeToken class</span></span>

<span data-ttu-id="1a9f3-212">Tek bir nesnede `IChangeToken` bir veya daha fazla <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> örneği temsil etmek için sınıfı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="1a9f3-213">`HasChanged`temsil edilen herhangi `true` bir belirteç `HasChanged` ise `true`bileşik belirteç raporları.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="1a9f3-214">`ActiveChangeCallbacks`temsil edilen herhangi `true` bir belirteç `ActiveChangeCallbacks` ise `true`bileşik belirteç raporları.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="1a9f3-215">Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri arama sı bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a9f3-216">*Değişiklik belirteci,* durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzeyli bir yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="1a9f3-217">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a9f3-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="1a9f3-218">IChangeToken arabirimi</span><span class="sxs-lookup"><span data-stu-id="1a9f3-218">IChangeToken interface</span></span>

<span data-ttu-id="1a9f3-219"><xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin meydana geldiğini bildirimleri yayır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="1a9f3-220">`IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a9f3-221">[Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmayan uygulamalar [için, Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="1a9f3-222">`IChangeToken`iki özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="1a9f3-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirteç leri proaktif olarak geri aramaları yükseltip yükseltmeyin.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="1a9f3-224">`ActiveChangedCallbacks` Ayarlanmışsa, geri arama asla çağrılmez ve uygulamanın değişiklikler için anket `HasChanged` yapması `false`gerekir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="1a9f3-225">Bir belirteci hiçbir değişiklik meydana gelmezse veya temel değişiklik dinleyicisi imha edilirse veya devre dışı bırakılırsa asla iptal edilmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="1a9f3-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişiklik gerçekleşip gerçekleşmediğini gösteren bir değer alır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="1a9f3-227">Arabirim, belirteç değiştiğinde çağrılan bir geri aramayı kaydeden [RegisterChangeCallback (Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `IChangeToken`</span><span class="sxs-lookup"><span data-stu-id="1a9f3-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="1a9f3-228">`HasChanged`geri arama çağrılmadan önce ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="1a9f3-229">ChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="1a9f3-229">ChangeToken class</span></span>

<span data-ttu-id="1a9f3-230"><xref:Microsoft.Extensions.Primitives.ChangeToken>bir değişikliğin meydana geldiği bildirimleri yaymak için kullanılan statik bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="1a9f3-231">`ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a9f3-232">[Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmayan uygulamalar [için, Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="1a9f3-233">[ChangeToken.OnChange(Func\<IChangeToken>, Eylem)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi `Action` belirteç değiştiğinde aramak için bir kayıt:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="1a9f3-234">`Func<IChangeToken>`belirteci üretir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="1a9f3-235">`Action`belirteç değiştiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="1a9f3-236">[ChangeToken.OnChange\<TState\<>(Func IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yük belirteç tüketici `TState` `Action`içine geçti ek bir parametre alır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="1a9f3-237">`OnChange`bir <xref:System.IDisposable>' yi döndürür.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="1a9f3-238">Arama, <xref:System.IDisposable.Dispose*> belirtecinin daha fazla değişiklik için dinlemesini durdurur ve belirteç kaynaklarını serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="1a9f3-239">ASP.NET Core'daki değişiklik belirteçlerinin örnek kullanımları</span><span class="sxs-lookup"><span data-stu-id="1a9f3-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1a9f3-240">Değişiklik belirteçleri, nesnelerdeki değişiklikleri izlemek için ASP.NET Core'un önemli alanlarında kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="1a9f3-241">Dosyalardaki değişiklikleri izlemek <xref:Microsoft.Extensions.FileProviders.IFileProvider>için, <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 'ın `IChangeToken` yöntemi, belirtilen dosyaların veya klasörün izlemesi için bir yöntem oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="1a9f3-242">`IChangeToken`belirteçleri, değişiklik üzerindeki önbellek tahliyelerini tetiklemek için önbellek girişlerine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="1a9f3-243">Değişiklikler `TOptions` için varsayılan <xref:Microsoft.Extensions.Options.OptionsMonitor`1> uygulama, bir veya daha fazla <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> örneği kabul eden aşırı yüklemeye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="1a9f3-244">Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için bir değişiklik bildirimi geri araması kaydetmek için bir geri döndürür.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="1a9f3-245">Yapılandırma değişiklikleri için monitör</span><span class="sxs-lookup"><span data-stu-id="1a9f3-245">Monitor for configuration changes</span></span>

<span data-ttu-id="1a9f3-246">Varsayılan olarak, ASP.NET Core şablonları [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) *(appsettings.json*, *appsettings) kullanır. Development.json*ve *uygulama ayarları. Production.json*) uygulama yapılandırma ayarlarını yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="1a9f3-247">Bu dosyalar [addjsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) uzantı yöntemi <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="1a9f3-248">`reloadOnChange`dosya değişikliklerinde yapılandırmanın yeniden yüklenmesi gerekiyorsa gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="1a9f3-249">Bu ayar <xref:Microsoft.AspNetCore.WebHost> kolaylık yönteminde <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>görünür:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="1a9f3-250">Dosya tabanlı yapılandırma <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="1a9f3-251">`FileConfigurationSource`dosyaları <xref:Microsoft.Extensions.FileProviders.IFileProvider> izlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="1a9f3-252">Varsayılan olarak, `IFileMonitor` yapılandırma dosyası <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>değişiklikleri <xref:System.IO.FileSystemWatcher> için izlemek için kullanan bir , tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="1a9f3-253">Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="1a9f3-254">Herhangi bir *uygulama ayarları* dosyaları değişirse, dosya izleme uygulamalarının her ikisi de özel kod&mdash;yürütmek örnek uygulama konsola bir mesaj yazar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="1a9f3-255">Yapılandırma dosyası, `FileSystemWatcher` tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri aramasını tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="1a9f3-256">Özel kodun birden çok belirteç geri araması tetiklendiğinde yalnızca bir kez çalıştırıldığından emin olmak için, örneğin uygulama dosyası işlenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="1a9f3-257">Örnek SHA1 dosya karma kullanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="1a9f3-258">Bir yeniden deneme üstel geri leme ile uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="1a9f3-259">Dosya kilitleme, dosyaüzerinde yeni bir karmanın bilgisayara kullanımını geçici olarak engelleyen oluşabilir, çünkü yeniden deneme vardır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="1a9f3-260">*Yardımcı Programlar/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="1a9f3-261">Basit başlangıç değişikliği belirteci</span><span class="sxs-lookup"><span data-stu-id="1a9f3-261">Simple startup change token</span></span>

<span data-ttu-id="1a9f3-262">Yapılandırma yeniden yükleme `Action` belirteci değişiklik bildirimleri için bir belirteç tüketici geri arama kaydedin.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="1a9f3-263">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="1a9f3-264">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="1a9f3-265">Geri arama `InvokeChanged` yöntemidir:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="1a9f3-266">Geri arama, izlemek için doğru `IHostingEnvironment`uygulama *ayarları* yapılandırma dosyasını (örneğin, uygulama ayarları) belirtmek için yararlı olan , içinde geçmek için kullanılır. `state` \* Development.json\* zaman Geliştirme ortamında).</span><span class="sxs-lookup"><span data-stu-id="1a9f3-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="1a9f3-267">Dosya işlenmeleri, yapılandırma `WriteConsole` dosyası yalnızca bir kez değiştiğinde birden çok belirteç geri araması nedeniyle deyimin birden çok kez çalışmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="1a9f3-268">Bu sistem, uygulama çalıştığı sürece çalışır ve kullanıcı tarafından devre dışı tutulamaz.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="1a9f3-269">Hizmet olarak yapılandırma değişikliklerini izleme</span><span class="sxs-lookup"><span data-stu-id="1a9f3-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="1a9f3-270">Örnek uygular:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-270">The sample implements:</span></span>

* <span data-ttu-id="1a9f3-271">Temel başlangıç belirteç izleme.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="1a9f3-272">Bir hizmet olarak izleme.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-272">Monitoring as a service.</span></span>
* <span data-ttu-id="1a9f3-273">İzlemeyi etkinleştirmek ve devre dışı kılabilir bir mekanizma.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="1a9f3-274">Örnek bir `IConfigurationMonitor` arayüz kurar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="1a9f3-275">*Uzantılar/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="1a9f3-276">Uygulanan sınıfın oluşturucusu, `ConfigurationMonitor`değişiklik bildirimleri için bir geri arama kaydeder:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="1a9f3-277">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="1a9f3-278">`InvokeChanged`geri arama yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="1a9f3-279">Bu `state` örnekte, izleme durumuna `IConfigurationMonitor` erişmek için kullanılan örneğin başvurusudur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="1a9f3-280">İki özellik kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-280">Two properties are used:</span></span>

* <span data-ttu-id="1a9f3-281">`MonitoringEnabled`&ndash; Geri aramanın özel kodunu çalıştırıp çalıştırmaması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-281">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="1a9f3-282">`CurrentState`&ndash; Kullanıcı Arabirimi'nde kullanılmak üzere geçerli izleme durumunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-282">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="1a9f3-283">Yöntem, `InvokeChanged` önceki yaklaşıma benzer, ancak bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="1a9f3-284">Kodu çalışmadığı sürece `MonitoringEnabled` `true`çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="1a9f3-285">`WriteConsole` Çıktısındaki akımı `state` çıkar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="1a9f3-286">Bir `ConfigurationMonitor` örnek bir hizmet `Startup.ConfigureServices`olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1a9f3-287">Dizin sayfası yapılandırma izleme üzerinde kullanıcı denetimi sunar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="1a9f3-288">Örneği içine `IConfigurationMonitor` enjekte `IndexModel`edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="1a9f3-289">*Sayfalar/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1a9f3-290">Yapılandırma monitörü`_monitor`( ) izlemeyi etkinleştirmek veya devre dışı kalmak ve Kullanıcı Arabirimi geri bildirimi için geçerli durumu ayarlamak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1a9f3-291">`OnPostStartMonitoring` Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="1a9f3-292">`OnPostStopMonitoring` Tetiklendiğinde, izleme devre dışı bırakılır ve durum izlemenin oluşmadığını yansıtacak şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="1a9f3-293">Kullanıcı UI'deki düğmeler izlemeyi etkinleştiriyor ve devre dışı bıtır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="1a9f3-294">*Sayfalar/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="1a9f3-295">Önbelleğe alınmış dosya değişikliklerini izleme</span><span class="sxs-lookup"><span data-stu-id="1a9f3-295">Monitor cached file changes</span></span>

<span data-ttu-id="1a9f3-296">Dosya içeriği bellekte önbelleğe <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>alınabilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="1a9f3-297">Bellek içi önbelleğe alma, [Bellek tesisi](xref:performance/caching/memory) başlığında açıklanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="1a9f3-298">Aşağıda açıklanan uygulama gibi ek adımlar atmadan, kaynak veri değişirse *eski* (eski) veriler önbellekten döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="1a9f3-299">Örneğin, [sürgülü](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) bir son kullanma süresini yenilerken önbelleğe alınmış bir kaynak dosyanın durumunu hesaba katmamak, eski önbelleğe alınmış dosya verilerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="1a9f3-300">Veriler için her istek kayan son kullanma süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="1a9f3-301">Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, muhtemelen eski içeriğe tabidir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="1a9f3-302">Dosya önbelleğe alma senaryosunda değişiklik belirteçleri kullanmak önbellekte eski dosya içeriğinin bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="1a9f3-303">Örnek uygulama, yaklaşımın bir uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="1a9f3-304">Örnek için `GetFileContent` kullanır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="1a9f3-305">Dosya içeriğini iade edin.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-305">Return file content.</span></span>
* <span data-ttu-id="1a9f3-306">Dosya kilidinin dosyanın okunmasını geçici olarak engellediği durumları kapsayacak şekilde üstel yedekleme içeren bir yeniden deneme algoritması uygulayın.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="1a9f3-307">*Yardımcı Programlar/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="1a9f3-308">Önbelleğe alınmış dosya aramalarını işlemek için a `FileService` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="1a9f3-309">Hizmetin `GetFileContent` yöntem çağrısı, bellek içi önbellekten dosya içeriğini elde etmeye ve arayana *(Hizmetler/FileService.cs)* döndürmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="1a9f3-310">Önbelleğe alınan içerik önbellek anahtarı kullanılarak bulunmazsa, aşağıdaki işlemler yapılır:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="1a9f3-311">Dosya içeriği kullanılarak `GetFileContent`elde edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="1a9f3-312">Bir değişiklik belirteci [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile dosya sağlayıcısından elde edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="1a9f3-313">Dosya değiştirildiğinde belirteç geri arama tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="1a9f3-314">Dosya içeriği sürgülü bir [son kullanma](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alındı.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="1a9f3-315">Dosya önbelleğe alınmışken değişirse önbellek girişini çıkarmak için [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte değişiklik belirteci eklenir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="1a9f3-316">Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)depolanır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="1a9f3-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) uygulamanın bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> işaret elde etmek <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="1a9f3-318">`filePath` [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="1a9f3-319">Bellek `FileService` önbelleğe alma hizmeti ile birlikte servis konteynerinde kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="1a9f3-320">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="1a9f3-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="1a9f3-321">Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="1a9f3-322">Dizin sayfasının `OnGet` yönteminde (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="1a9f3-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="1a9f3-323">CompositeChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="1a9f3-323">CompositeChangeToken class</span></span>

<span data-ttu-id="1a9f3-324">Tek bir nesnede `IChangeToken` bir veya daha fazla <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> örneği temsil etmek için sınıfı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="1a9f3-325">`HasChanged`temsil edilen herhangi `true` bir belirteç `HasChanged` ise `true`bileşik belirteç raporları.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="1a9f3-326">`ActiveChangeCallbacks`temsil edilen herhangi `true` bir belirteç `ActiveChangeCallbacks` ise `true`bileşik belirteç raporları.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="1a9f3-327">Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri arama sı bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1a9f3-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1a9f3-328">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1a9f3-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
