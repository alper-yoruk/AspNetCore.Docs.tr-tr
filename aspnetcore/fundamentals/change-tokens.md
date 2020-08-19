---
title: ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla
author: rick-anderson
description: Değişiklikleri izlemek için değişiklik belirteçlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: 891cce975c5852b0192fd7ff22b21060d1dac8ac
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634923"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="7cf72-103">ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla</span><span class="sxs-lookup"><span data-stu-id="7cf72-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7cf72-104">*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="7cf72-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7cf72-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="7cf72-106">Ichangetoken arabirimi</span><span class="sxs-lookup"><span data-stu-id="7cf72-106">IChangeToken interface</span></span>

<span data-ttu-id="7cf72-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> bir değişikliğin gerçekleştiği bildirimleri yayar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="7cf72-108">`IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7cf72-109">[Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="7cf72-110">`IChangeToken` iki özelliğe sahiptir:</span><span class="sxs-lookup"><span data-stu-id="7cf72-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="7cf72-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="7cf72-112">`ActiveChangedCallbacks`Olarak ayarlanırsa `false` , bir geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="7cf72-113">Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="7cf72-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="7cf72-115">`IChangeToken`Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (eylem \<Object> , nesne)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="7cf72-116">`HasChanged` geri çağırma çağrılmadan önce ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="7cf72-117">ChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="7cf72-117">ChangeToken class</span></span>

<span data-ttu-id="7cf72-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> , bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="7cf72-119">`ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7cf72-120">[Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="7cf72-121">[ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, `Action` belirteç her değiştiğinde bir çağrı yapar:</span><span class="sxs-lookup"><span data-stu-id="7cf72-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="7cf72-122">`Func<IChangeToken>` belirteci üretir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="7cf72-123">`Action` belirteç değiştiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="7cf72-124">[ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, `TState` belirteç tüketicisine geçirilen ek bir parametreyi alır `Action` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="7cf72-125">`OnChange` döndürür <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="7cf72-126">Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="7cf72-127">ASP.NET Core değişiklik belirteçlerinin örnek kullanımları</span><span class="sxs-lookup"><span data-stu-id="7cf72-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="7cf72-128">Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7cf72-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="7cf72-129">Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi `IChangeToken` belirtilen dosya veya klasör için bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="7cf72-130">`IChangeToken` değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="7cf72-131">`TOptions`Değişiklikler için, varsayılan uygulamasının <xref:Microsoft.Extensions.Options.OptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bir veya daha fazla örnek kabul eden bir aşırı yüklemesi vardır <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="7cf72-132">Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="7cf72-133">Yapılandırma değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="7cf72-133">Monitor for configuration changes</span></span>

<span data-ttu-id="7cf72-134">ASP.NET Core şablonlar, uygulama yapılandırma ayarlarını yüklemek için varsayılan olarak [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.js*, *appsettings.Development.jsaçık*ve *appsettings.Production.jsüzerinde*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="7cf72-135">Bu dosyalar, üzerinde bir parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="7cf72-136">`reloadOnChange` yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="7cf72-137">Bu ayar <xref:Microsoft.Extensions.Hosting.Host> kolaylık yönteminde görünür <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="7cf72-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="7cf72-138">Dosya tabanlı yapılandırma tarafından temsil edilir <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="7cf72-139">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>dosyalarını izlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="7cf72-140">Varsayılan olarak, `IFileMonitor` <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> <xref:System.IO.FileSystemWatcher> yapılandırma dosyası değişikliklerini izlemek için kullanılan bir tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="7cf72-141">Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="7cf72-142">*AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod yürütür &mdash; örnek uygulama konsola bir ileti yazar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="7cf72-143">Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="7cf72-144">Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="7cf72-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="7cf72-145">Örnek, SHA1 dosya karma kullanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="7cf72-146">Bir yeniden deneme, üstel geri dönme ile uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="7cf72-147">Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="7cf72-148">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="7cf72-149">Basit başlangıç değiştirme belirteci</span><span class="sxs-lookup"><span data-stu-id="7cf72-149">Simple startup change token</span></span>

<span data-ttu-id="7cf72-150">`Action`Değişiklik bildirimleri için bir belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7cf72-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="7cf72-151">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="7cf72-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="7cf72-152">`config.GetReloadToken()` belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="7cf72-153">Geri çağırma `InvokeChanged` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7cf72-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="7cf72-154">`state`Geri aramanın, `IWebHostEnvironment` izlemek için doğru *appSettings* yapılandırma dosyasını (örneğin, geliştirme ortamında olduğu zaman *appsettings.Development.js* ) belirtmek için yararlı olan ' a geçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="7cf72-155">Dosya karmaları, `WriteConsole` bir yapılandırma dosyası yalnızca bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="7cf72-156">Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.</span><span class="sxs-lookup"><span data-stu-id="7cf72-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="7cf72-157">Yapılandırma değişikliklerini hizmet olarak izle</span><span class="sxs-lookup"><span data-stu-id="7cf72-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="7cf72-158">Örnek şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="7cf72-158">The sample implements:</span></span>

* <span data-ttu-id="7cf72-159">Temel başlangıç belirteci izleme.</span><span class="sxs-lookup"><span data-stu-id="7cf72-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="7cf72-160">Hizmet olarak izleme.</span><span class="sxs-lookup"><span data-stu-id="7cf72-160">Monitoring as a service.</span></span>
* <span data-ttu-id="7cf72-161">İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.</span><span class="sxs-lookup"><span data-stu-id="7cf72-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="7cf72-162">Örnek bir arabirim oluşturur `IConfigurationMonitor` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="7cf72-163">*Uzantılar/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="7cf72-164">Uygulanan sınıfın Oluşturucusu, `ConfigurationMonitor` değişiklik bildirimleri için bir geri çağırma kaydeder:</span><span class="sxs-lookup"><span data-stu-id="7cf72-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="7cf72-165">`config.GetReloadToken()` belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="7cf72-166">`InvokeChanged` geri çağırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="7cf72-167">`state`Bu örnekte, `IConfigurationMonitor` izleme durumuna erişmek için kullanılan örneğe bir başvuru vardır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="7cf72-168">İki özellik kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7cf72-168">Two properties are used:</span></span>

* <span data-ttu-id="7cf72-169">`MonitoringEnabled`: Geri aramanın özel kodunu çalıştırması gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="7cf72-170">`CurrentState`: Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="7cf72-171">`InvokeChanged`Yöntemi önceki yaklaşımla benzerdir, bunun dışında:</span><span class="sxs-lookup"><span data-stu-id="7cf72-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="7cf72-172">, Olmadığı müddetçe kodunu çalıştırmaz `MonitoringEnabled` `true` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="7cf72-173">`state`Çıktıda geçerli olan çıktıyı verir `WriteConsole` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="7cf72-174">Bir örnek `ConfigurationMonitor` , içinde bir hizmet olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7cf72-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="7cf72-175">Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="7cf72-176">Örneği `IConfigurationMonitor` öğesine eklenir `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="7cf72-177">*Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7cf72-178">Yapılandırma İzleyicisi ( `_monitor` ), izlemeyi etkinleştirmek veya devre dışı bırakmak ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7cf72-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="7cf72-179">`OnPostStartMonitoring`Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="7cf72-180">`OnPostStopMonitoring`Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="7cf72-181">Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="7cf72-182">*Sayfa/dizin. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="7cf72-183">Önbelleğe alınmış dosya değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="7cf72-183">Monitor cached file changes</span></span>

<span data-ttu-id="7cf72-184">Dosya içeriği, kullanarak bellek içinde önbelleğe alınabilir <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="7cf72-185">Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="7cf72-186">Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="7cf72-187">Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="7cf72-188">Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="7cf72-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="7cf72-189">Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="7cf72-190">Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller.</span><span class="sxs-lookup"><span data-stu-id="7cf72-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="7cf72-191">Örnek uygulama, yaklaşımın bir uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="7cf72-192">Örnek şu amaçlarla kullanılır `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="7cf72-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="7cf72-193">Dosya içeriğini döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-193">Return file content.</span></span>
* <span data-ttu-id="7cf72-194">Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7cf72-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="7cf72-195">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="7cf72-196">`FileService`Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="7cf72-197">`GetFileContent`Hizmetin Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="7cf72-198">Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="7cf72-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="7cf72-199">Dosya içeriği kullanılarak elde edilir `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="7cf72-200">Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="7cf72-201">Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="7cf72-202">Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="7cf72-203">Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="7cf72-204">Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="7cf72-205">`IWebHostEnvironment.ContentRootFileProvider` , <xref:Microsoft.Extensions.FileProviders.IFileProvider> uygulamanın üzerine gelindiğinde bir işaret elde etmek için kullanılır `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="7cf72-206">, `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="7cf72-207">, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="7cf72-208">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="7cf72-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="7cf72-209">Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.</span><span class="sxs-lookup"><span data-stu-id="7cf72-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="7cf72-210">Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7cf72-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="7cf72-211">CompositeChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="7cf72-211">CompositeChangeToken class</span></span>

<span data-ttu-id="7cf72-212">Tek bir nesnedeki bir veya daha fazla örneği temsil etmek için `IChangeToken` sınıfını kullanın <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="7cf72-213">`HasChanged``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="7cf72-214">`ActiveChangeCallbacks``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="7cf72-215">Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7cf72-216">*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="7cf72-217">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7cf72-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="7cf72-218">Ichangetoken arabirimi</span><span class="sxs-lookup"><span data-stu-id="7cf72-218">IChangeToken interface</span></span>

<span data-ttu-id="7cf72-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> bir değişikliğin gerçekleştiği bildirimleri yayar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="7cf72-220">`IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7cf72-221">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7cf72-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="7cf72-222">`IChangeToken` iki özelliğe sahiptir:</span><span class="sxs-lookup"><span data-stu-id="7cf72-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="7cf72-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="7cf72-224">`ActiveChangedCallbacks`Olarak ayarlanırsa `false` , bir geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="7cf72-225">Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="7cf72-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="7cf72-227">`IChangeToken`Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (eylem \<Object> , nesne)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="7cf72-228">`HasChanged` geri çağırma çağrılmadan önce ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="7cf72-229">ChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="7cf72-229">ChangeToken class</span></span>

<span data-ttu-id="7cf72-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> , bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="7cf72-231">`ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7cf72-232">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7cf72-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="7cf72-233">[ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, `Action` belirteç her değiştiğinde bir çağrı yapar:</span><span class="sxs-lookup"><span data-stu-id="7cf72-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="7cf72-234">`Func<IChangeToken>` belirteci üretir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="7cf72-235">`Action` belirteç değiştiğinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="7cf72-236">[ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, `TState` belirteç tüketicisine geçirilen ek bir parametreyi alır `Action` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="7cf72-237">`OnChange` döndürür <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="7cf72-238">Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="7cf72-239">ASP.NET Core değişiklik belirteçlerinin örnek kullanımları</span><span class="sxs-lookup"><span data-stu-id="7cf72-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="7cf72-240">Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7cf72-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="7cf72-241">Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi `IChangeToken` belirtilen dosya veya klasör için bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="7cf72-242">`IChangeToken` değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="7cf72-243">`TOptions`Değişiklikler için, varsayılan uygulamasının <xref:Microsoft.Extensions.Options.OptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bir veya daha fazla örnek kabul eden bir aşırı yüklemesi vardır <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="7cf72-244">Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="7cf72-245">Yapılandırma değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="7cf72-245">Monitor for configuration changes</span></span>

<span data-ttu-id="7cf72-246">ASP.NET Core şablonlar, uygulama yapılandırma ayarlarını yüklemek için varsayılan olarak [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.js*, *appsettings.Development.jsaçık*ve *appsettings.Production.jsüzerinde*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="7cf72-247">Bu dosyalar, üzerinde bir parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="7cf72-248">`reloadOnChange` yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="7cf72-249">Bu ayar <xref:Microsoft.AspNetCore.WebHost> kolaylık yönteminde görünür <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="7cf72-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="7cf72-250">Dosya tabanlı yapılandırma tarafından temsil edilir <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="7cf72-251">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>dosyalarını izlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="7cf72-252">Varsayılan olarak, `IFileMonitor` <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> <xref:System.IO.FileSystemWatcher> yapılandırma dosyası değişikliklerini izlemek için kullanılan bir tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="7cf72-253">Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="7cf72-254">*AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod yürütür &mdash; örnek uygulama konsola bir ileti yazar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="7cf72-255">Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="7cf72-256">Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="7cf72-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="7cf72-257">Örnek, SHA1 dosya karma kullanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="7cf72-258">Bir yeniden deneme, üstel geri dönme ile uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="7cf72-259">Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="7cf72-260">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="7cf72-261">Basit başlangıç değiştirme belirteci</span><span class="sxs-lookup"><span data-stu-id="7cf72-261">Simple startup change token</span></span>

<span data-ttu-id="7cf72-262">`Action`Değişiklik bildirimleri için bir belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7cf72-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="7cf72-263">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="7cf72-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="7cf72-264">`config.GetReloadToken()` belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="7cf72-265">Geri çağırma `InvokeChanged` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7cf72-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="7cf72-266">`state`Geri aramanın, `IHostingEnvironment` izlemek için doğru *appSettings* yapılandırma dosyasını (örneğin, geliştirme ortamında olduğu zaman *appsettings.Development.js* ) belirtmek için yararlı olan ' a geçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="7cf72-267">Dosya karmaları, `WriteConsole` bir yapılandırma dosyası yalnızca bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="7cf72-268">Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.</span><span class="sxs-lookup"><span data-stu-id="7cf72-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="7cf72-269">Yapılandırma değişikliklerini hizmet olarak izle</span><span class="sxs-lookup"><span data-stu-id="7cf72-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="7cf72-270">Örnek şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="7cf72-270">The sample implements:</span></span>

* <span data-ttu-id="7cf72-271">Temel başlangıç belirteci izleme.</span><span class="sxs-lookup"><span data-stu-id="7cf72-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="7cf72-272">Hizmet olarak izleme.</span><span class="sxs-lookup"><span data-stu-id="7cf72-272">Monitoring as a service.</span></span>
* <span data-ttu-id="7cf72-273">İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.</span><span class="sxs-lookup"><span data-stu-id="7cf72-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="7cf72-274">Örnek bir arabirim oluşturur `IConfigurationMonitor` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="7cf72-275">*Uzantılar/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="7cf72-276">Uygulanan sınıfın Oluşturucusu, `ConfigurationMonitor` değişiklik bildirimleri için bir geri çağırma kaydeder:</span><span class="sxs-lookup"><span data-stu-id="7cf72-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="7cf72-277">`config.GetReloadToken()` belirteci sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="7cf72-278">`InvokeChanged` geri çağırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="7cf72-279">`state`Bu örnekte, `IConfigurationMonitor` izleme durumuna erişmek için kullanılan örneğe bir başvuru vardır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="7cf72-280">İki özellik kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7cf72-280">Two properties are used:</span></span>

* <span data-ttu-id="7cf72-281">`MonitoringEnabled`: Geri aramanın özel kodunu çalıştırması gerekip gerekmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="7cf72-282">`CurrentState`: Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="7cf72-283">`InvokeChanged`Yöntemi önceki yaklaşımla benzerdir, bunun dışında:</span><span class="sxs-lookup"><span data-stu-id="7cf72-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="7cf72-284">, Olmadığı müddetçe kodunu çalıştırmaz `MonitoringEnabled` `true` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="7cf72-285">`state`Çıktıda geçerli olan çıktıyı verir `WriteConsole` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="7cf72-286">Bir örnek `ConfigurationMonitor` , içinde bir hizmet olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7cf72-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="7cf72-287">Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="7cf72-288">Örneği `IConfigurationMonitor` öğesine eklenir `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="7cf72-289">*Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7cf72-290">Yapılandırma İzleyicisi ( `_monitor` ), izlemeyi etkinleştirmek veya devre dışı bırakmak ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7cf72-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="7cf72-291">`OnPostStartMonitoring`Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="7cf72-292">`OnPostStopMonitoring`Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="7cf72-293">Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="7cf72-294">*Sayfa/dizin. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="7cf72-295">Önbelleğe alınmış dosya değişikliklerini izle</span><span class="sxs-lookup"><span data-stu-id="7cf72-295">Monitor cached file changes</span></span>

<span data-ttu-id="7cf72-296">Dosya içeriği, kullanarak bellek içinde önbelleğe alınabilir <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="7cf72-297">Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="7cf72-298">Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="7cf72-299">Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="7cf72-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="7cf72-300">Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="7cf72-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="7cf72-301">Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="7cf72-302">Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller.</span><span class="sxs-lookup"><span data-stu-id="7cf72-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="7cf72-303">Örnek uygulama, yaklaşımın bir uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="7cf72-304">Örnek şu amaçlarla kullanılır `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="7cf72-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="7cf72-305">Dosya içeriğini döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cf72-305">Return file content.</span></span>
* <span data-ttu-id="7cf72-306">Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7cf72-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="7cf72-307">*Yardımcı programlar/yardımcı programlar. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf72-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="7cf72-308">`FileService`Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7cf72-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="7cf72-309">`GetFileContent`Hizmetin Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="7cf72-310">Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="7cf72-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="7cf72-311">Dosya içeriği kullanılarak elde edilir `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="7cf72-312">Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="7cf72-313">Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="7cf72-314">Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="7cf72-315">Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="7cf72-316">Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="7cf72-317">[Ihostingenvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) <xref:Microsoft.Extensions.FileProviders.IFileProvider> , uygulamanın üzerine gelindiğinde bir işaret elde etmek için kullanılır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="7cf72-318">, `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="7cf72-319">, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7cf72-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="7cf72-320">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="7cf72-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="7cf72-321">Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.</span><span class="sxs-lookup"><span data-stu-id="7cf72-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="7cf72-322">Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7cf72-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="7cf72-323">CompositeChangeToken sınıfı</span><span class="sxs-lookup"><span data-stu-id="7cf72-323">CompositeChangeToken class</span></span>

<span data-ttu-id="7cf72-324">Tek bir nesnedeki bir veya daha fazla örneği temsil etmek için `IChangeToken` sınıfını kullanın <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="7cf72-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="7cf72-325">`HasChanged``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="7cf72-326">`ActiveChangeCallbacks``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="7cf72-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="7cf72-327">Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7cf72-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7cf72-328">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7cf72-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
