---
<span data-ttu-id="c2699-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2699-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2699-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2699-102">'Blazor'</span></span>
- <span data-ttu-id="c2699-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2699-103">'Identity'</span></span>
- <span data-ttu-id="c2699-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2699-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2699-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2699-105">'Razor'</span></span>
- <span data-ttu-id="c2699-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2699-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="c2699-107">ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla</span><span class="sxs-lookup"><span data-stu-id="c2699-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2699-108">*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="c2699-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="c2699-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2699-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="c2699-110">Ichangetoken arabirimi</span><span class="sxs-lookup"><span data-stu-id="c2699-110">IChangeToken interface</span></span>

<span data-ttu-id="c2699-111"><xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin gerçekleştiği bildirimleri yayar.</span><span class="sxs-lookup"><span data-stu-id="c2699-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="c2699-112">`IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="c2699-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c2699-113">[Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="c2699-114">`IChangeToken`iki özelliğe sahiptir:</span><span class="sxs-lookup"><span data-stu-id="c2699-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="c2699-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c2699-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="c2699-116">`ActiveChangedCallbacks`Olarak ayarlanırsa `false` , bir geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="c2699-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="c2699-117">Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.</span><span class="sxs-lookup"><span data-stu-id="c2699-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="c2699-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.</span><span class="sxs-lookup"><span data-stu-id="c2699-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="c2699-119">`IChangeToken`Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (eylem \<Object> , nesne)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir.</span><span class="sxs-lookup"><span data-stu-id="c2699-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="c2699-120">`HasChanged`geri çağırma çağrılmadan önce ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c2699-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="c2699-121">ChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="c2699-121">ChangeToken class</span></span>

<span data-ttu-id="c2699-122"><xref:Microsoft.Extensions.Primitives.ChangeToken>, bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c2699-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="c2699-123">`ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="c2699-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c2699-124">[Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="c2699-125">[ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, `Action` belirteç her değiştiğinde bir çağrı yapar:</span><span class="sxs-lookup"><span data-stu-id="c2699-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="c2699-126">`Func<IChangeToken>`belirteci üretir.</span><span class="sxs-lookup"><span data-stu-id="c2699-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="c2699-127">`Action`belirteç değiştiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c2699-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="c2699-128">[ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, `TState` belirteç tüketicisine geçirilen ek bir parametreyi alır `Action` .</span><span class="sxs-lookup"><span data-stu-id="c2699-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="c2699-129">`OnChange`döndürür <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="c2699-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="c2699-130">Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="c2699-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="c2699-131">ASP.NET Core değişiklik belirteçlerinin örnek kullanımları</span><span class="sxs-lookup"><span data-stu-id="c2699-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c2699-132">Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2699-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="c2699-133">Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi `IChangeToken` belirtilen dosya veya klasör için bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c2699-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="c2699-134">`IChangeToken`değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="c2699-135">`TOptions`Değişiklikler için, varsayılan uygulamasının <xref:Microsoft.Extensions.Options.OptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bir veya daha fazla örnek kabul eden bir aşırı yüklemesi vardır <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> .</span><span class="sxs-lookup"><span data-stu-id="c2699-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="c2699-136">Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="c2699-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="c2699-137">Yapılandırma değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="c2699-137">Monitor for configuration changes</span></span>

<span data-ttu-id="c2699-138">Varsayılan olarak, ASP.NET Core şablonlar [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, appSettings) kullanır *. Development. JSON*ve *appSettings. Üretim. JSON*), uygulama yapılandırma ayarlarını yükler.</span><span class="sxs-lookup"><span data-stu-id="c2699-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="c2699-139">Bu dosyalar, üzerinde bir parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` .</span><span class="sxs-lookup"><span data-stu-id="c2699-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="c2699-140">`reloadOnChange`yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="c2699-141">Bu ayar <xref:Microsoft.Extensions.Hosting.Host> kolaylık yönteminde görünür <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="c2699-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="c2699-142">Dosya tabanlı yapılandırma tarafından temsil edilir <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="c2699-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="c2699-143">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>dosyalarını izlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="c2699-144">Varsayılan olarak, `IFileMonitor` <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> <xref:System.IO.FileSystemWatcher> yapılandırma dosyası değişikliklerini izlemek için kullanılan bir tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="c2699-145">Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="c2699-146">*AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod yürütür &mdash; örnek uygulama konsola bir ileti yazar.</span><span class="sxs-lookup"><span data-stu-id="c2699-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="c2699-147">Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="c2699-148">Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="c2699-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="c2699-149">Örnek, SHA1 dosya karma kullanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="c2699-150">Bir yeniden deneme, üstel geri dönme ile uygulanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="c2699-151">Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.</span><span class="sxs-lookup"><span data-stu-id="c2699-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="c2699-152">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="c2699-153">Basit başlangıç değiştirme belirteci</span><span class="sxs-lookup"><span data-stu-id="c2699-153">Simple startup change token</span></span>

<span data-ttu-id="c2699-154">`Action`Değişiklik bildirimleri için bir belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c2699-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="c2699-155">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="c2699-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="c2699-156">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2699-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="c2699-157">Geri çağırma `InvokeChanged` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="c2699-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="c2699-158">`state`Geri aramanın, `IWebHostEnvironment` izlemek için doğru *appSettings* yapılandırma dosyasını (örneğin, appSettings) belirtmek için yararlı olan ' a geçmek için kullanılır *. Geliştirme ortamında geliştirme. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="c2699-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="c2699-159">Dosya karmaları, `WriteConsole` bir yapılandırma dosyası yalnızca bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2699-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="c2699-160">Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.</span><span class="sxs-lookup"><span data-stu-id="c2699-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="c2699-161">Yapılandırma değişikliklerini hizmet olarak izle</span><span class="sxs-lookup"><span data-stu-id="c2699-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="c2699-162">Örnek şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="c2699-162">The sample implements:</span></span>

* <span data-ttu-id="c2699-163">Temel başlangıç belirteci izleme.</span><span class="sxs-lookup"><span data-stu-id="c2699-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="c2699-164">Hizmet olarak izleme.</span><span class="sxs-lookup"><span data-stu-id="c2699-164">Monitoring as a service.</span></span>
* <span data-ttu-id="c2699-165">İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.</span><span class="sxs-lookup"><span data-stu-id="c2699-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="c2699-166">Örnek bir arabirim oluşturur `IConfigurationMonitor` .</span><span class="sxs-lookup"><span data-stu-id="c2699-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="c2699-167">*Uzantılar/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="c2699-168">Uygulanan sınıfın Oluşturucusu, `ConfigurationMonitor` değişiklik bildirimleri için bir geri çağırma kaydeder:</span><span class="sxs-lookup"><span data-stu-id="c2699-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="c2699-169">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2699-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="c2699-170">`InvokeChanged`geri çağırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="c2699-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="c2699-171">`state`Bu örnekte, `IConfigurationMonitor` izleme durumuna erişmek için kullanılan örneğe bir başvuru vardır.</span><span class="sxs-lookup"><span data-stu-id="c2699-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="c2699-172">İki özellik kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2699-172">Two properties are used:</span></span>

* <span data-ttu-id="c2699-173">`MonitoringEnabled`: Geri aramanın özel kodunu çalıştırması gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="c2699-174">`CurrentState`: Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="c2699-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="c2699-175">`InvokeChanged`Yöntemi önceki yaklaşımla benzerdir, bunun dışında:</span><span class="sxs-lookup"><span data-stu-id="c2699-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="c2699-176">, Olmadığı müddetçe kodunu çalıştırmaz `MonitoringEnabled` `true` .</span><span class="sxs-lookup"><span data-stu-id="c2699-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="c2699-177">`state`Çıktıda geçerli olan çıktıyı verir `WriteConsole` .</span><span class="sxs-lookup"><span data-stu-id="c2699-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="c2699-178">Bir örnek `ConfigurationMonitor` , içinde bir hizmet olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c2699-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c2699-179">Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2699-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="c2699-180">Örneği `IConfigurationMonitor` öğesine eklenir `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="c2699-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="c2699-181">*Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c2699-182">Yapılandırma İzleyicisi ( `_monitor` ), izlemeyi etkinleştirmek veya devre dışı bırakmak ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2699-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="c2699-183">`OnPostStartMonitoring`Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir.</span><span class="sxs-lookup"><span data-stu-id="c2699-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="c2699-184">`OnPostStopMonitoring`Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="c2699-185">Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="c2699-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="c2699-186">*Sayfa/dizin. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c2699-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="c2699-187">Önbelleğe alınmış dosya değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="c2699-187">Monitor cached file changes</span></span>

<span data-ttu-id="c2699-188">Dosya içeriği, kullanarak bellek içinde önbelleğe alınabilir <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="c2699-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="c2699-189">Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c2699-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="c2699-190">Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.</span><span class="sxs-lookup"><span data-stu-id="c2699-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="c2699-191">Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="c2699-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="c2699-192">Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="c2699-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="c2699-193">Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.</span><span class="sxs-lookup"><span data-stu-id="c2699-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="c2699-194">Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller.</span><span class="sxs-lookup"><span data-stu-id="c2699-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="c2699-195">Örnek uygulama, yaklaşımın bir uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="c2699-196">Örnek şu amaçlarla kullanılır `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="c2699-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="c2699-197">Dosya içeriğini döndürür.</span><span class="sxs-lookup"><span data-stu-id="c2699-197">Return file content.</span></span>
* <span data-ttu-id="c2699-198">Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c2699-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="c2699-199">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="c2699-200">`FileService`Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c2699-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="c2699-201">`GetFileContent`Hizmetin Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="c2699-202">Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="c2699-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="c2699-203">Dosya içeriği kullanılarak elde edilir `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="c2699-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="c2699-204">Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="c2699-205">Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="c2699-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="c2699-206">Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="c2699-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="c2699-207">Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.</span><span class="sxs-lookup"><span data-stu-id="c2699-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="c2699-208">Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="c2699-209">`IWebHostEnvironment.ContentRootFileProvider`, <xref:Microsoft.Extensions.FileProviders.IFileProvider> uygulamanın üzerine gelindiğinde bir işaret elde etmek için kullanılır `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="c2699-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="c2699-210">, `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="c2699-211">, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="c2699-212">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="c2699-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="c2699-213">Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.</span><span class="sxs-lookup"><span data-stu-id="c2699-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="c2699-214">Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="c2699-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="c2699-215">CompositeChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="c2699-215">CompositeChangeToken class</span></span>

<span data-ttu-id="c2699-216">Tek bir nesnedeki bir veya daha fazla örneği temsil etmek için `IChangeToken` sınıfını kullanın <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="c2699-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="c2699-217">`HasChanged``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="c2699-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="c2699-218">`ActiveChangeCallbacks``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="c2699-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="c2699-219">Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c2699-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2699-220">*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="c2699-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="c2699-221">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2699-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="c2699-222">Ichangetoken arabirimi</span><span class="sxs-lookup"><span data-stu-id="c2699-222">IChangeToken interface</span></span>

<span data-ttu-id="c2699-223"><xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin gerçekleştiği bildirimleri yayar.</span><span class="sxs-lookup"><span data-stu-id="c2699-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="c2699-224">`IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="c2699-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c2699-225">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c2699-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="c2699-226">`IChangeToken`iki özelliğe sahiptir:</span><span class="sxs-lookup"><span data-stu-id="c2699-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="c2699-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c2699-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="c2699-228">`ActiveChangedCallbacks`Olarak ayarlanırsa `false` , bir geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="c2699-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="c2699-229">Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.</span><span class="sxs-lookup"><span data-stu-id="c2699-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="c2699-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.</span><span class="sxs-lookup"><span data-stu-id="c2699-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="c2699-231">`IChangeToken`Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (eylem \<Object> , nesne)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir.</span><span class="sxs-lookup"><span data-stu-id="c2699-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="c2699-232">`HasChanged`geri çağırma çağrılmadan önce ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c2699-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="c2699-233">ChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="c2699-233">ChangeToken class</span></span>

<span data-ttu-id="c2699-234"><xref:Microsoft.Extensions.Primitives.ChangeToken>, bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c2699-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="c2699-235">`ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="c2699-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c2699-236">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c2699-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="c2699-237">[ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, `Action` belirteç her değiştiğinde bir çağrı yapar:</span><span class="sxs-lookup"><span data-stu-id="c2699-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="c2699-238">`Func<IChangeToken>`belirteci üretir.</span><span class="sxs-lookup"><span data-stu-id="c2699-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="c2699-239">`Action`belirteç değiştiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c2699-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="c2699-240">[ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, `TState` belirteç tüketicisine geçirilen ek bir parametreyi alır `Action` .</span><span class="sxs-lookup"><span data-stu-id="c2699-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="c2699-241">`OnChange`döndürür <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="c2699-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="c2699-242">Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="c2699-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="c2699-243">ASP.NET Core değişiklik belirteçlerinin örnek kullanımları</span><span class="sxs-lookup"><span data-stu-id="c2699-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c2699-244">Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2699-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="c2699-245">Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi `IChangeToken` belirtilen dosya veya klasör için bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c2699-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="c2699-246">`IChangeToken`değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="c2699-247">`TOptions`Değişiklikler için, varsayılan uygulamasının <xref:Microsoft.Extensions.Options.OptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bir veya daha fazla örnek kabul eden bir aşırı yüklemesi vardır <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> .</span><span class="sxs-lookup"><span data-stu-id="c2699-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="c2699-248">Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="c2699-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="c2699-249">Yapılandırma değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="c2699-249">Monitor for configuration changes</span></span>

<span data-ttu-id="c2699-250">Varsayılan olarak, ASP.NET Core şablonlar [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, appSettings) kullanır *. Development. JSON*ve *appSettings. Üretim. JSON*), uygulama yapılandırma ayarlarını yükler.</span><span class="sxs-lookup"><span data-stu-id="c2699-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="c2699-251">Bu dosyalar, üzerinde bir parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` .</span><span class="sxs-lookup"><span data-stu-id="c2699-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="c2699-252">`reloadOnChange`yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="c2699-253">Bu ayar <xref:Microsoft.AspNetCore.WebHost> kolaylık yönteminde görünür <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="c2699-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="c2699-254">Dosya tabanlı yapılandırma tarafından temsil edilir <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="c2699-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="c2699-255">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>dosyalarını izlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="c2699-256">Varsayılan olarak, `IFileMonitor` <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> <xref:System.IO.FileSystemWatcher> yapılandırma dosyası değişikliklerini izlemek için kullanılan bir tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="c2699-257">Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="c2699-258">*AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod yürütür &mdash; örnek uygulama konsola bir ileti yazar.</span><span class="sxs-lookup"><span data-stu-id="c2699-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="c2699-259">Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="c2699-260">Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="c2699-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="c2699-261">Örnek, SHA1 dosya karma kullanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="c2699-262">Bir yeniden deneme, üstel geri dönme ile uygulanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="c2699-263">Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.</span><span class="sxs-lookup"><span data-stu-id="c2699-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="c2699-264">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="c2699-265">Basit başlangıç değiştirme belirteci</span><span class="sxs-lookup"><span data-stu-id="c2699-265">Simple startup change token</span></span>

<span data-ttu-id="c2699-266">`Action`Değişiklik bildirimleri için bir belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c2699-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="c2699-267">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="c2699-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="c2699-268">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2699-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="c2699-269">Geri çağırma `InvokeChanged` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="c2699-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="c2699-270">`state`Geri aramanın, `IHostingEnvironment` izlemek için doğru *appSettings* yapılandırma dosyasını (örneğin, appSettings) belirtmek için yararlı olan ' a geçmek için kullanılır *. Geliştirme ortamında geliştirme. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="c2699-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="c2699-271">Dosya karmaları, `WriteConsole` bir yapılandırma dosyası yalnızca bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2699-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="c2699-272">Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.</span><span class="sxs-lookup"><span data-stu-id="c2699-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="c2699-273">Yapılandırma değişikliklerini hizmet olarak izle</span><span class="sxs-lookup"><span data-stu-id="c2699-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="c2699-274">Örnek şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="c2699-274">The sample implements:</span></span>

* <span data-ttu-id="c2699-275">Temel başlangıç belirteci izleme.</span><span class="sxs-lookup"><span data-stu-id="c2699-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="c2699-276">Hizmet olarak izleme.</span><span class="sxs-lookup"><span data-stu-id="c2699-276">Monitoring as a service.</span></span>
* <span data-ttu-id="c2699-277">İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.</span><span class="sxs-lookup"><span data-stu-id="c2699-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="c2699-278">Örnek bir arabirim oluşturur `IConfigurationMonitor` .</span><span class="sxs-lookup"><span data-stu-id="c2699-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="c2699-279">*Uzantılar/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="c2699-280">Uygulanan sınıfın Oluşturucusu, `ConfigurationMonitor` değişiklik bildirimleri için bir geri çağırma kaydeder:</span><span class="sxs-lookup"><span data-stu-id="c2699-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="c2699-281">`config.GetReloadToken()`belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2699-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="c2699-282">`InvokeChanged`geri çağırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="c2699-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="c2699-283">`state`Bu örnekte, `IConfigurationMonitor` izleme durumuna erişmek için kullanılan örneğe bir başvuru vardır.</span><span class="sxs-lookup"><span data-stu-id="c2699-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="c2699-284">İki özellik kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2699-284">Two properties are used:</span></span>

* <span data-ttu-id="c2699-285">`MonitoringEnabled`: Geri aramanın özel kodunu çalıştırması gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="c2699-286">`CurrentState`: Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="c2699-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="c2699-287">`InvokeChanged`Yöntemi önceki yaklaşımla benzerdir, bunun dışında:</span><span class="sxs-lookup"><span data-stu-id="c2699-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="c2699-288">, Olmadığı müddetçe kodunu çalıştırmaz `MonitoringEnabled` `true` .</span><span class="sxs-lookup"><span data-stu-id="c2699-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="c2699-289">`state`Çıktıda geçerli olan çıktıyı verir `WriteConsole` .</span><span class="sxs-lookup"><span data-stu-id="c2699-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="c2699-290">Bir örnek `ConfigurationMonitor` , içinde bir hizmet olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c2699-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c2699-291">Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2699-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="c2699-292">Örneği `IConfigurationMonitor` öğesine eklenir `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="c2699-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="c2699-293">*Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c2699-294">Yapılandırma İzleyicisi ( `_monitor` ), izlemeyi etkinleştirmek veya devre dışı bırakmak ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2699-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="c2699-295">`OnPostStartMonitoring`Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir.</span><span class="sxs-lookup"><span data-stu-id="c2699-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="c2699-296">`OnPostStopMonitoring`Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="c2699-297">Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="c2699-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="c2699-298">*Sayfa/dizin. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c2699-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="c2699-299">Önbelleğe alınmış dosya değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="c2699-299">Monitor cached file changes</span></span>

<span data-ttu-id="c2699-300">Dosya içeriği, kullanarak bellek içinde önbelleğe alınabilir <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="c2699-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="c2699-301">Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c2699-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="c2699-302">Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.</span><span class="sxs-lookup"><span data-stu-id="c2699-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="c2699-303">Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="c2699-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="c2699-304">Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="c2699-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="c2699-305">Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.</span><span class="sxs-lookup"><span data-stu-id="c2699-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="c2699-306">Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller.</span><span class="sxs-lookup"><span data-stu-id="c2699-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="c2699-307">Örnek uygulama, yaklaşımın bir uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2699-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="c2699-308">Örnek şu amaçlarla kullanılır `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="c2699-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="c2699-309">Dosya içeriğini döndürür.</span><span class="sxs-lookup"><span data-stu-id="c2699-309">Return file content.</span></span>
* <span data-ttu-id="c2699-310">Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c2699-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="c2699-311">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2699-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="c2699-312">`FileService`Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c2699-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="c2699-313">`GetFileContent`Hizmetin Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="c2699-314">Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="c2699-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="c2699-315">Dosya içeriği kullanılarak elde edilir `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="c2699-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="c2699-316">Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="c2699-317">Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="c2699-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="c2699-318">Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="c2699-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="c2699-319">Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.</span><span class="sxs-lookup"><span data-stu-id="c2699-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="c2699-320">Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır.</span><span class="sxs-lookup"><span data-stu-id="c2699-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="c2699-321">[Ihostingenvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) <xref:Microsoft.Extensions.FileProviders.IFileProvider> , uygulamanın üzerine gelindiğinde bir işaret elde etmek için kullanılır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="c2699-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="c2699-322">, `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="c2699-323">, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c2699-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="c2699-324">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="c2699-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="c2699-325">Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.</span><span class="sxs-lookup"><span data-stu-id="c2699-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="c2699-326">Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="c2699-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="c2699-327">CompositeChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="c2699-327">CompositeChangeToken class</span></span>

<span data-ttu-id="c2699-328">Tek bir nesnedeki bir veya daha fazla örneği temsil etmek için `IChangeToken` sınıfını kullanın <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="c2699-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="c2699-329">`HasChanged``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="c2699-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="c2699-330">`ActiveChangeCallbacks``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="c2699-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="c2699-331">Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c2699-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c2699-332">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c2699-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
