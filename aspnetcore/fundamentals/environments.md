---
title: ASP.NET Core'da birden çok ortam kullanma
author: rick-anderson
description: ASP.NET Core uygulamalarında birden çok ortamda uygulama davranışını denetlemeyi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330666"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="455af-103">ASP.NET Core'da birden çok ortam kullanma</span><span class="sxs-lookup"><span data-stu-id="455af-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="455af-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="455af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="455af-105">ASP.NET Core, bir ortam değişkeni kullanarak çalışma zamanı ortamı temelinde uygulama davranışını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="455af-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="455af-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="455af-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="455af-107">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="455af-107">Environments</span></span>

<span data-ttu-id="455af-108">Çalışma zamanı ortamını öğrenmek için ASP.NET Core aşağıdaki ortam değişkenlerinden okur:</span><span class="sxs-lookup"><span data-stu-id="455af-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="455af-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="455af-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="455af-110">`ASPNETCORE_ENVIRONMENT`ne zaman <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağrılır.</span><span class="sxs-lookup"><span data-stu-id="455af-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="455af-111">Varsayılan ASP.NET Core Web uygulaması şablonları çağrısı `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="455af-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="455af-112">`ASPNETCORE_ENVIRONMENT`Değer geçersiz kılınır `DOTNET_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="455af-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="455af-113">`IHostEnvironment.EnvironmentName`herhangi bir değere ayarlanabilir, ancak aşağıdaki değerler Framework tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="455af-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="455af-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: Dosya [üzerindelaunchSettings.js](#lsj) `ASPNETCORE_ENVIRONMENT` `Development` Yerel makinede olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="455af-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: Varsayılan if `DOTNET_ENVIRONMENT` ve `ASPNETCORE_ENVIRONMENT` ayarlanmamışsa.</span><span class="sxs-lookup"><span data-stu-id="455af-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="455af-116">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="455af-116">The following code:</span></span>

* <span data-ttu-id="455af-117">Olarak ayarlandığında [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) çağırır `ASPNETCORE_ENVIRONMENT` `Development` .</span><span class="sxs-lookup"><span data-stu-id="455af-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="455af-118">Değeri, veya olarak ayarlandığında [Useexceptionhandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) `ASPNETCORE_ENVIRONMENT` ' i çağırır `Staging` `Production` `Staging_2` .</span><span class="sxs-lookup"><span data-stu-id="455af-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="455af-119">İçine çıkarır <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="455af-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="455af-120">Bu yaklaşım, uygulama yalnızca `Startup.Configure` , ortam başına en az kod farklılığı olan birkaç ortam için ayarlama gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="455af-121">, ASP.NET Core şablonları tarafından oluşturulan koda benzerdir.</span><span class="sxs-lookup"><span data-stu-id="455af-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="455af-122">[Ortam etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) , öğesinde biçimlendirme eklemek veya dışlamak Için [ıhostenvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) değerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="455af-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="455af-123">[Örnek koddan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) [hakkında sayfası](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) , önceki biçimlendirmeyi içerir ve değerini görüntüler `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="455af-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="455af-124">Windows ve macOS 'ta, ortam değişkenleri ve değerleri büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="455af-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="455af-125">Linux ortam değişkenleri ve değerleri varsayılan olarak **büyük/küçük harfe duyarlıdır** .</span><span class="sxs-lookup"><span data-stu-id="455af-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="455af-126">EnvironmentsSample oluşturma</span><span class="sxs-lookup"><span data-stu-id="455af-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="455af-127">Bu belgede kullanılan [örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) , Razor *environmentssample*adlı bir sayfalar projesini temel alır.</span><span class="sxs-lookup"><span data-stu-id="455af-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="455af-128">Aşağıdaki kod *Environmentssample*adlı bir Web uygulaması oluşturur ve çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="455af-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="455af-129">Uygulama çalıştığında, aşağıdaki çıkışın bazılarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="455af-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="455af-130">Geliştirme ve launchSettings.js</span><span class="sxs-lookup"><span data-stu-id="455af-130">Development and launchSettings.json</span></span>

<span data-ttu-id="455af-131">Geliştirme ortamı, üretimde gösterilmemelidir özellikleri etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="455af-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="455af-132">Örneğin, ASP.NET Core Şablonlar geliştirme ortamında [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="455af-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="455af-133">Yerel makine geliştirme ortamı, projenin dosyasında *Properties\launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="455af-134">Sistem ortamında ayarlanan geçersiz kılma değerlerinde *launchSettings.js* ayarlanan ortam değerleri.</span><span class="sxs-lookup"><span data-stu-id="455af-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="455af-135">Dosyadaki *launchSettings.js* :</span><span class="sxs-lookup"><span data-stu-id="455af-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="455af-136">Yalnızca yerel geliştirme makinesinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="455af-137">Dağıtılmadı.</span><span class="sxs-lookup"><span data-stu-id="455af-137">Is not deployed.</span></span>
* <span data-ttu-id="455af-138">profil ayarlarını içerir.</span><span class="sxs-lookup"><span data-stu-id="455af-138">contains profile settings.</span></span>

<span data-ttu-id="455af-139">Aşağıdaki JSON, Visual Studio ile oluşturulan ASP.NET Core bir Web tahmini adlı *Environmentssample* adlı dosyada *launchSettings.js* göstermektedir `dotnet new` :</span><span class="sxs-lookup"><span data-stu-id="455af-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="455af-140">Yukarıdaki biçimlendirme iki profil içerir:</span><span class="sxs-lookup"><span data-stu-id="455af-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="455af-141">`IIS Express`: Uygulama Visual Studio 'dan başlatılırken kullanılan varsayılan profil.</span><span class="sxs-lookup"><span data-stu-id="455af-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="455af-142">`"commandName"`Anahtarın değeri bulunur `"IISExpress"` , bu nedenle [iisexpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="455af-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="455af-143">Başlatma profilini projeye veya dahil edilen başka bir profile ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="455af-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="455af-144">Örneğin, aşağıdaki görüntüde, proje adının seçilmesi [Kestrel Web sunucusunu](xref:fundamentals/servers/kestrel)başlatır.</span><span class="sxs-lookup"><span data-stu-id="455af-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![Menüdeki IIS Express Başlat](environments/_static/iisx2.png)
* <span data-ttu-id="455af-146">`EnvironmentsSample`: Profil adı proje adıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="455af-147">Bu profil, uygulamasının ile başlatılması sırasında varsayılan olarak kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="455af-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="455af-148">`"commandName"`Anahtarın değeri bulunur `"Project"` , bu nedenle [Kestrel Web sunucusu](xref:fundamentals/servers/kestrel) başlatılır.</span><span class="sxs-lookup"><span data-stu-id="455af-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="455af-149">Değeri, `commandName` başlatılacak Web sunucusunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="455af-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="455af-150">`commandName`aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="455af-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="455af-151">`IISExpress`: IIS Express başlatır.</span><span class="sxs-lookup"><span data-stu-id="455af-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="455af-152">`IIS`: Web sunucusu başlatılmadı.</span><span class="sxs-lookup"><span data-stu-id="455af-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="455af-153">IIS 'nin kullanılabilir olması bekleniyor.</span><span class="sxs-lookup"><span data-stu-id="455af-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="455af-154">`Project`: Kestrel başlatır.</span><span class="sxs-lookup"><span data-stu-id="455af-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="455af-155">Visual Studio proje özellikleri **hata ayıklama** sekmesi, dosyadaki *launchSettings.js* düzenlemek için bir GUI sağlar.</span><span class="sxs-lookup"><span data-stu-id="455af-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="455af-156">Proje profillerinde yapılan değişiklikler, Web sunucusu yeniden başlatılana kadar etkili olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="455af-157">Kestrel, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Proje özellikleri ayar ortamı değişkenleri](environments/_static/project-properties-debug.png)

<span data-ttu-id="455af-159">Dosyadaki şu *launchSettings.js* birden çok profil var:</span><span class="sxs-lookup"><span data-stu-id="455af-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="455af-160">Profiller seçilebilir:</span><span class="sxs-lookup"><span data-stu-id="455af-160">Profiles can be selected:</span></span>

* <span data-ttu-id="455af-161">Visual Studio kullanıcı arabiriminden.</span><span class="sxs-lookup"><span data-stu-id="455af-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="455af-162">Komutu [`dotnet run`](/dotnet/core/tools/dotnet-run) bir komut kabuğu 'nda `--launch-profile` profil adına ayarlanmış seçeneğiyle kullanma.</span><span class="sxs-lookup"><span data-stu-id="455af-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="455af-163">*Bu yaklaşım yalnızca Kestrel profillerini destekler.*</span><span class="sxs-lookup"><span data-stu-id="455af-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="455af-164">*launchSettings.js* , gizli dizileri depolamamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="455af-165">Gizli anahtar geliştirme için gizli dizileri depolamak için [gizli dizi Yöneticisi aracı](xref:security/app-secrets) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="455af-166">[Visual Studio Code](https://code.visualstudio.com/)kullanırken, ortam değişkenleri dosyadaki *. vscode/launch.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="455af-167">Aşağıdaki örnek, çeşitli [konak yapılandırma değerleri ortam değişkenlerini](xref:fundamentals/host/web-host#host-configuration-values)ayarlar:</span><span class="sxs-lookup"><span data-stu-id="455af-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="455af-168">Dosyadaki *. vscode/launch.js* yalnızca Visual Studio Code tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="455af-169">Üretim</span><span class="sxs-lookup"><span data-stu-id="455af-169">Production</span></span>

<span data-ttu-id="455af-170">Üretim ortamının güvenliği, [performansı](xref:performance/performance-best-practices)ve uygulama sağlamlık düzeyini en üst düzeye çıkarmak için yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="455af-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="455af-171">Geliştirmeden farklı bazı yaygın ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="455af-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="455af-172">[Önbelleğe alma](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="455af-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="455af-173">İstemci tarafı kaynaklar paketlenmiş, küçültülmüş ve potansiyel olarak bir CDN 'den sunulan.</span><span class="sxs-lookup"><span data-stu-id="455af-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="455af-174">Tanılama hata sayfaları devre dışı.</span><span class="sxs-lookup"><span data-stu-id="455af-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="455af-175">Kolay hata sayfaları etkin.</span><span class="sxs-lookup"><span data-stu-id="455af-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="455af-176">Üretim [günlüğü](xref:fundamentals/logging/index) ve izleme etkin.</span><span class="sxs-lookup"><span data-stu-id="455af-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="455af-177">Örneğin, [Application Insights](/azure/application-insights/app-insights-asp-net-core)kullanma.</span><span class="sxs-lookup"><span data-stu-id="455af-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="455af-178">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="455af-178">Set the environment</span></span>

<span data-ttu-id="455af-179">Bir ortam değişkeni veya platform ayarıyla test için belirli bir ortam ayarlamak genellikle yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="455af-180">Ortam ayarlanmamışsa, `Production` çoğu hata ayıklama özelliğini devre dışı bırakan varsayılan olarak olur.</span><span class="sxs-lookup"><span data-stu-id="455af-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="455af-181">Ortamı ayarlama yöntemi işletim sistemine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="455af-182">Konak yapılandırıldığında, uygulama tarafından okunan son ortam ayarı, uygulamanın ortamını belirler.</span><span class="sxs-lookup"><span data-stu-id="455af-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="455af-183">Uygulama çalışırken uygulamanın ortamı değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="455af-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="455af-184">[Örnek koddan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) [hakkında sayfasında](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) değerini gösterir `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="455af-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="455af-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="455af-185">Azure App Service</span></span>

<span data-ttu-id="455af-186">[Azure App Service](https://azure.microsoft.com/services/app-service/)ortamında ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="455af-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="455af-187">Uygulama **Hizmetleri** dikey penceresinden uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="455af-188">**Ayarlar** grubunda **yapılandırma** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="455af-189">**Uygulama ayarları** sekmesinde **Yeni uygulama ayarı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="455af-190">**Uygulama ayarı Ekle/Düzenle** penceresinde ad ' ı belirtin `ASPNETCORE_ENVIRONMENT` . **Name**</span><span class="sxs-lookup"><span data-stu-id="455af-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="455af-191">**Değer**için ortamı sağlayın (örneğin, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="455af-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="455af-192">Dağıtım yuvaları takas edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **dağıtım yuvası ayarını** onay kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="455af-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="455af-193">Daha fazla bilgi için bkz. Azure belgelerindeki [Azure App Service hazırlama ortamlarını ayarlama](/azure/app-service/web-sites-staged-publishing) .</span><span class="sxs-lookup"><span data-stu-id="455af-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="455af-194">**Tamam ' ı** seçerek **uygulama ayarı Ekle/Düzenle** penceresini kapatın.</span><span class="sxs-lookup"><span data-stu-id="455af-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="455af-195">**Yapılandırma** dikey penceresinin en üstünde **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="455af-196">Azure App Service, Azure portal bir uygulama ayarı eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="455af-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="455af-197">Windows</span><span class="sxs-lookup"><span data-stu-id="455af-197">Windows</span></span>

<span data-ttu-id="455af-198">Sistem ortamında ayarlanan geçersiz kılma değerleri *üzerindelaunchSettings.js* ortam değerleri.</span><span class="sxs-lookup"><span data-stu-id="455af-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="455af-199">`ASPNETCORE_ENVIRONMENT`Uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)kullanılarak başlatıldığında geçerli oturum için öğesini ayarlamak için aşağıdaki komutlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="455af-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="455af-200">**Komut istemi**</span><span class="sxs-lookup"><span data-stu-id="455af-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="455af-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="455af-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="455af-202">Önceki komut `ASPNETCORE_ENVIRONMENT` yalnızca bu komut penceresinden başlatılan süreçler için ayarlar.</span><span class="sxs-lookup"><span data-stu-id="455af-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="455af-203">Windows 'da genel değeri ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="455af-204">**Denetim Masası** > **sistem** > **Gelişmiş sistem ayarları** ' nı açın ve değeri ekleyin veya düzenleyin `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="455af-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Sistem Gelişmiş Özellikler](environments/_static/systemsetting_environment.png)

  ![ASPNET Core ortam değişkeni](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="455af-207">Bir yönetim komut istemi açın ve komutunu kullanın `setx` veya bir yönetim PowerShell komut istemi açın ve şunu kullanın `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="455af-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="455af-208">**Komut istemi**</span><span class="sxs-lookup"><span data-stu-id="455af-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="455af-209">`/M`Anahtar, ortam değişkenini sistem düzeyinde ayarlamaya yönelik olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="455af-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="455af-210">`/M`Anahtar kullanılmazsa, ortam değişkeni Kullanıcı hesabı için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="455af-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="455af-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="455af-212">`Machine`Seçenek değeri, ortam değişkeninin sistem düzeyinde ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="455af-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="455af-213">Seçenek değeri olarak değiştirilirse `User` , ortam değişkeni Kullanıcı hesabı için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="455af-214">`ASPNETCORE_ENVIRONMENT`Ortam değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde için geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="455af-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="455af-215">Sistem ortamında ayarlanan geçersiz kılma değerleri *üzerindelaunchSettings.js* ortam değerleri.</span><span class="sxs-lookup"><span data-stu-id="455af-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="455af-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="455af-216">**web.config**</span></span>

<span data-ttu-id="455af-217">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini *web.config*olarak ayarlamak için, *ortam değişkenlerini ayarlama* bölümüne bakın <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="455af-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="455af-218">**Proje dosyası veya yayımlama profili**</span><span class="sxs-lookup"><span data-stu-id="455af-218">**Project file or publish profile**</span></span>

<span data-ttu-id="455af-219">**WINDOWS IIS dağıtımları için:** `<EnvironmentName>`Özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="455af-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="455af-220">Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="455af-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="455af-221">**IIS uygulama havuzu başına**</span><span class="sxs-lookup"><span data-stu-id="455af-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="455af-222">`ASPNETCORE_ENVIRONMENT`Yalıtılmış bir uygulama havuzunda çalışan bir uygulamanın ortam değişkenini ayarlamak için (ııs 10,0 veya üzeri sürümlerde desteklenir), [ortam değişkenleri &lt; &gt; environmentvariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="455af-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="455af-223">`ASPNETCORE_ENVIRONMENT`Ortam değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="455af-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="455af-224">IIS 'de bir uygulama barındırırken ve ortam değişkenini eklerken ya da değiştirirken `ASPNETCORE_ENVIRONMENT` , yeni değerin uygulamalar tarafından çekilmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="455af-225">' İ `net stop was /y` `net start w3svc` komut isteminden sonra yürütün.</span><span class="sxs-lookup"><span data-stu-id="455af-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="455af-226">Sunucuyu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="455af-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="455af-227">macOS</span><span class="sxs-lookup"><span data-stu-id="455af-227">macOS</span></span>

<span data-ttu-id="455af-228">MacOS için geçerli ortamın ayarlanması, uygulamayı çalıştırırken satır içinde gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="455af-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="455af-229">Alternatif olarak, `export` uygulamayı çalıştırmadan önce ortamı olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="455af-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="455af-230">Makine düzeyinde ortam değişkenleri *. bashrc* veya *. bash_profile* dosyasında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="455af-231">Herhangi bir metin düzenleyicisini kullanarak dosyayı düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="455af-231">Edit the file using any text editor.</span></span> <span data-ttu-id="455af-232">Aşağıdaki ifadeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="455af-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="455af-233">Linux</span><span class="sxs-lookup"><span data-stu-id="455af-233">Linux</span></span>

<span data-ttu-id="455af-234">Linux dağıtımları için, `export` oturum tabanlı değişken ayarları ve makine düzeyi ortam ayarları için *bash_profile* dosyası için komut isteminde komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="455af-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="455af-235">Kodda ortam ayarlama</span><span class="sxs-lookup"><span data-stu-id="455af-235">Set the environment in code</span></span>

<span data-ttu-id="455af-236"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>Ana bilgisayarı derlerken çağırın.</span><span class="sxs-lookup"><span data-stu-id="455af-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="455af-237">Bkz. <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="455af-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="455af-238">Ortama göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="455af-238">Configuration by environment</span></span>

<span data-ttu-id="455af-239">Yapılandırmayı ortama göre yüklemek için bkz <xref:fundamentals/configuration/index#json-configuration-provider> ..</span><span class="sxs-lookup"><span data-stu-id="455af-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="455af-240">Ortam tabanlı başlangıç sınıfı ve yöntemleri</span><span class="sxs-lookup"><span data-stu-id="455af-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="455af-241">Başlangıç sınıfına ıwebhostenvironment ekleme</span><span class="sxs-lookup"><span data-stu-id="455af-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="455af-242"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` Oluşturucuya ekleme.</span><span class="sxs-lookup"><span data-stu-id="455af-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="455af-243">Bu yaklaşım, uygulama `Startup` her ortam için en az kod farklılığı olan birkaç ortam için yapılandırma gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="455af-244">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="455af-244">In the following example:</span></span>

* <span data-ttu-id="455af-245">Ortam `_env` alanında tutulur.</span><span class="sxs-lookup"><span data-stu-id="455af-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="455af-246">`_env`, ve ' de, `ConfigureServices` `Configure` uygulamanın ortamına göre başlangıç yapılandırması uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="455af-247">Başlangıç sınıfı kuralları</span><span class="sxs-lookup"><span data-stu-id="455af-247">Startup class conventions</span></span>

<span data-ttu-id="455af-248">ASP.NET Core bir uygulama başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyükleme.</span><span class="sxs-lookup"><span data-stu-id="455af-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="455af-249">Uygulama, `Startup` farklı ortamlar için birden çok sınıf tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="455af-250">Çalışma zamanında uygun `Startup` sınıf seçildi.</span><span class="sxs-lookup"><span data-stu-id="455af-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="455af-251">Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir.</span><span class="sxs-lookup"><span data-stu-id="455af-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="455af-252">Eşleşen bir `Startup{EnvironmentName}` sınıf bulunamazsa `Startup` sınıf kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="455af-253">Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="455af-254">Tipik uygulamalar bu yaklaşıma ihtiyaç duymayacak.</span><span class="sxs-lookup"><span data-stu-id="455af-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="455af-255">Ortam tabanlı sınıfları uygulamak için `Startup` bir `Startup{EnvironmentName}` sınıflar ve geri dönüş `Startup` sınıfı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="455af-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="455af-256">Derleme adını kabul eden [Usestartup (ıwebhostbuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="455af-257">Başlangıç yöntemi kuralları</span><span class="sxs-lookup"><span data-stu-id="455af-257">Startup method conventions</span></span>

<span data-ttu-id="455af-258">[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , formun ve ortama özgü sürümlerini destekler `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="455af-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="455af-259">Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="455af-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="455af-260">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="455af-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="455af-261">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="455af-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="455af-262">ASP.NET Core, bir ortam değişkeni kullanarak çalışma zamanı ortamı temelinde uygulama davranışını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="455af-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="455af-263">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="455af-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="455af-264">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="455af-264">Environments</span></span>

<span data-ttu-id="455af-265">ASP.NET Core, uygulama başlangıcında ortam değişkenini okur `ASPNETCORE_ENVIRONMENT` ve değeri [ıhostingenvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="455af-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="455af-266">`ASPNETCORE_ENVIRONMENT`herhangi bir değere ayarlanabilir, ancak Framework tarafından üç değer sağlanır:</span><span class="sxs-lookup"><span data-stu-id="455af-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="455af-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>varsayılanını</span><span class="sxs-lookup"><span data-stu-id="455af-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="455af-268">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="455af-268">The preceding code:</span></span>

* <span data-ttu-id="455af-269">Olarak ayarlandığında [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) çağırır `ASPNETCORE_ENVIRONMENT` `Development` .</span><span class="sxs-lookup"><span data-stu-id="455af-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="455af-270">Değeri aşağıdakilerden birini ayarlandığında [Useexceptionhandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) ' i çağırır `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="455af-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="455af-271">[Ortam etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) , `IHostingEnvironment.EnvironmentName` öğesinde biçimlendirme eklemek veya dışlamak için değerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="455af-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="455af-272">Windows ve macOS 'ta, ortam değişkenleri ve değerleri büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="455af-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="455af-273">Linux ortam değişkenleri ve değerleri varsayılan olarak büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="455af-274">Geliştirme</span><span class="sxs-lookup"><span data-stu-id="455af-274">Development</span></span>

<span data-ttu-id="455af-275">Geliştirme ortamı, üretimde gösterilmemelidir özellikleri etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="455af-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="455af-276">Örneğin, ASP.NET Core Şablonlar geliştirme ortamında [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="455af-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="455af-277">Yerel makine geliştirme ortamı, projenin dosyasında *Properties\launchSettings.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="455af-278">Sistem ortamında ayarlanan geçersiz kılma değerlerinde *launchSettings.js* ayarlanan ortam değerleri.</span><span class="sxs-lookup"><span data-stu-id="455af-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="455af-279">Aşağıdaki JSON dosyadaki bir *launchSettings.js* üç profil göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="455af-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="455af-280">`applicationUrl` *launchSettings.jsüzerindeki* özelliği sunucu URL 'lerinin bir listesini belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="455af-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="455af-281">Listedeki URL 'Ler arasında noktalı virgül kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="455af-282">Uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)ile başlatıldığında, ile ilk profil `"commandName": "Project"` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="455af-283">Değeri, `commandName` başlatılacak Web sunucusunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="455af-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="455af-284">`commandName`aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="455af-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="455af-285">`Project`(Kestrel Başlatan)</span><span class="sxs-lookup"><span data-stu-id="455af-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="455af-286">Bir uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)ile başlatıldığında:</span><span class="sxs-lookup"><span data-stu-id="455af-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="455af-287">*launchSettings.json* , varsa okundu.</span><span class="sxs-lookup"><span data-stu-id="455af-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="455af-288">`environmentVariables`geçersiz kılma ortamı değişkenlerinde *launchSettings.js* ayarları.</span><span class="sxs-lookup"><span data-stu-id="455af-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="455af-289">Barındırma ortamı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="455af-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="455af-290">Aşağıdaki çıktıda, [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)ile başlatılan bir uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="455af-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="455af-291">Visual Studio proje özellikleri **hata ayıklama** sekmesi, dosyadaki *launchSettings.js* düzenlemek için bir GUI sağlar:</span><span class="sxs-lookup"><span data-stu-id="455af-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proje özellikleri ayar ortamı değişkenleri](environments/_static/project-properties-debug.png)

<span data-ttu-id="455af-293">Proje profillerinde yapılan değişiklikler, Web sunucusu yeniden başlatılana kadar etkili olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="455af-294">Kestrel, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="455af-295">*launchSettings.js* , gizli dizileri depolamamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="455af-296">Gizli anahtar geliştirme için gizli dizileri depolamak için [gizli dizi Yöneticisi aracı](xref:security/app-secrets) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="455af-297">[Visual Studio Code](https://code.visualstudio.com/)kullanırken, ortam değişkenleri dosyadaki *. vscode/launch.js* ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="455af-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="455af-298">Aşağıdaki örnek, ortamı şu şekilde ayarlar `Development` :</span><span class="sxs-lookup"><span data-stu-id="455af-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="455af-299">Projedeki bir *. vscode/launch.js* , uygulama, `dotnet run` *Özellikler/launchSettings.js*ile aynı şekilde başlatılırken okunamaz.</span><span class="sxs-lookup"><span data-stu-id="455af-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="455af-300">Geliştirmede *launchSettings.js* olmayan bir uygulama başlatırken, ortama bir ortam değişkeni veya bir komut satırı bağımsız değişkeni ile ayarlayın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="455af-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="455af-301">Üretim</span><span class="sxs-lookup"><span data-stu-id="455af-301">Production</span></span>

<span data-ttu-id="455af-302">Üretim ortamının güvenliği, performansı ve uygulama sağlamlık düzeyini en üst düzeye çıkarmak için yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="455af-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="455af-303">Geliştirmeden farklı bazı yaygın ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="455af-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="455af-304">Önbelleği.</span><span class="sxs-lookup"><span data-stu-id="455af-304">Caching.</span></span>
* <span data-ttu-id="455af-305">İstemci tarafı kaynaklar paketlenmiş, küçültülmüş ve potansiyel olarak bir CDN 'den sunulan.</span><span class="sxs-lookup"><span data-stu-id="455af-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="455af-306">Tanılama hata sayfaları devre dışı.</span><span class="sxs-lookup"><span data-stu-id="455af-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="455af-307">Kolay hata sayfaları etkin.</span><span class="sxs-lookup"><span data-stu-id="455af-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="455af-308">Üretim günlüğü ve izleme etkin.</span><span class="sxs-lookup"><span data-stu-id="455af-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="455af-309">Örneğin, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="455af-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="455af-310">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="455af-310">Set the environment</span></span>

<span data-ttu-id="455af-311">Bir ortam değişkeni veya platform ayarıyla test için belirli bir ortam ayarlamak genellikle yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="455af-312">Ortam ayarlanmamışsa, `Production` çoğu hata ayıklama özelliğini devre dışı bırakan varsayılan olarak olur.</span><span class="sxs-lookup"><span data-stu-id="455af-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="455af-313">Ortamı ayarlama yöntemi işletim sistemine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="455af-314">Konak yapılandırıldığında, uygulama tarafından okunan son ortam ayarı, uygulamanın ortamını belirler.</span><span class="sxs-lookup"><span data-stu-id="455af-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="455af-315">Uygulama çalışırken uygulamanın ortamı değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="455af-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="455af-316">Ortam değişkeni veya platform ayarı</span><span class="sxs-lookup"><span data-stu-id="455af-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="455af-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="455af-317">Azure App Service</span></span>

<span data-ttu-id="455af-318">[Azure App Service](https://azure.microsoft.com/services/app-service/)ortamında ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="455af-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="455af-319">Uygulama **Hizmetleri** dikey penceresinden uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="455af-320">**Ayarlar** grubunda **yapılandırma** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="455af-321">**Uygulama ayarları** sekmesinde **Yeni uygulama ayarı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="455af-322">**Uygulama ayarı Ekle/Düzenle** penceresinde ad ' ı belirtin `ASPNETCORE_ENVIRONMENT` . **Name**</span><span class="sxs-lookup"><span data-stu-id="455af-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="455af-323">**Değer**için ortamı sağlayın (örneğin, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="455af-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="455af-324">Dağıtım yuvaları takas edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **dağıtım yuvası ayarını** onay kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="455af-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="455af-325">Daha fazla bilgi için bkz. Azure belgelerindeki [Azure App Service hazırlama ortamlarını ayarlama](/azure/app-service/web-sites-staged-publishing) .</span><span class="sxs-lookup"><span data-stu-id="455af-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="455af-326">**Tamam ' ı** seçerek **uygulama ayarı Ekle/Düzenle** penceresini kapatın.</span><span class="sxs-lookup"><span data-stu-id="455af-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="455af-327">**Yapılandırma** dikey penceresinin en üstünde **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="455af-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="455af-328">Azure App Service, Azure portal bir uygulama ayarı (ortam değişkeni) eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="455af-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="455af-329">Windows</span><span class="sxs-lookup"><span data-stu-id="455af-329">Windows</span></span>

<span data-ttu-id="455af-330">`ASPNETCORE_ENVIRONMENT`Uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)kullanılarak başlatıldığında geçerli oturum için öğesini ayarlamak için aşağıdaki komutlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="455af-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="455af-331">**Komut istemi**</span><span class="sxs-lookup"><span data-stu-id="455af-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="455af-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="455af-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="455af-333">Bu komutlar yalnızca geçerli pencere için etkili olur.</span><span class="sxs-lookup"><span data-stu-id="455af-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="455af-334">Pencere kapatıldığında, `ASPNETCORE_ENVIRONMENT` ayar varsayılan ayar veya makine değerine döner.</span><span class="sxs-lookup"><span data-stu-id="455af-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="455af-335">Windows 'da genel değeri ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="455af-336">**Denetim Masası** > **sistem** > **Gelişmiş sistem ayarları** ' nı açın ve değeri ekleyin veya düzenleyin `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="455af-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Sistem Gelişmiş Özellikler](environments/_static/systemsetting_environment.png)

  ![ASPNET Core ortam değişkeni](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="455af-339">Bir yönetim komut istemi açın ve komutunu kullanın `setx` veya bir yönetim PowerShell komut istemi açın ve şunu kullanın `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="455af-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="455af-340">**Komut istemi**</span><span class="sxs-lookup"><span data-stu-id="455af-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="455af-341">`/M`Anahtar, ortam değişkenini sistem düzeyinde ayarlamaya yönelik olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="455af-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="455af-342">`/M`Anahtar kullanılmazsa, ortam değişkeni Kullanıcı hesabı için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="455af-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="455af-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="455af-344">`Machine`Seçenek değeri, ortam değişkeninin sistem düzeyinde ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="455af-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="455af-345">Seçenek değeri olarak değiştirilirse `User` , ortam değişkeni Kullanıcı hesabı için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="455af-346">`ASPNETCORE_ENVIRONMENT`Ortam değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde için geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="455af-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="455af-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="455af-347">**web.config**</span></span>

<span data-ttu-id="455af-348">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini *web.config*olarak ayarlamak için, *ortam değişkenlerini ayarlama* bölümüne bakın <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="455af-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="455af-349">**Proje dosyası veya yayımlama profili**</span><span class="sxs-lookup"><span data-stu-id="455af-349">**Project file or publish profile**</span></span>

<span data-ttu-id="455af-350">**WINDOWS IIS dağıtımları için:** `<EnvironmentName>`Özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="455af-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="455af-351">Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="455af-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="455af-352">**IIS uygulama havuzu başına**</span><span class="sxs-lookup"><span data-stu-id="455af-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="455af-353">`ASPNETCORE_ENVIRONMENT`Yalıtılmış bir uygulama havuzunda çalışan bir uygulamanın ortam değişkenini ayarlamak için (ııs 10,0 veya üzeri sürümlerde desteklenir), [ortam değişkenleri &lt; &gt; environmentvariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="455af-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="455af-354">`ASPNETCORE_ENVIRONMENT`Ortam değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="455af-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="455af-355">IIS 'de bir uygulama barındırırken ve ortam değişkenini eklerken ya da değiştirirken `ASPNETCORE_ENVIRONMENT` , yeni değerin uygulamalar tarafından çekilmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="455af-356">' İ `net stop was /y` `net start w3svc` komut isteminden sonra yürütün.</span><span class="sxs-lookup"><span data-stu-id="455af-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="455af-357">Sunucuyu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="455af-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="455af-358">macOS</span><span class="sxs-lookup"><span data-stu-id="455af-358">macOS</span></span>

<span data-ttu-id="455af-359">MacOS için geçerli ortamın ayarlanması, uygulamayı çalıştırırken satır içinde gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="455af-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="455af-360">Alternatif olarak, `export` uygulamayı çalıştırmadan önce ortamı olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="455af-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="455af-361">Makine düzeyinde ortam değişkenleri *. bashrc* veya *. bash_profile* dosyasında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="455af-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="455af-362">Herhangi bir metin düzenleyicisini kullanarak dosyayı düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="455af-362">Edit the file using any text editor.</span></span> <span data-ttu-id="455af-363">Aşağıdaki ifadeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="455af-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="455af-364">Linux</span><span class="sxs-lookup"><span data-stu-id="455af-364">Linux</span></span>

<span data-ttu-id="455af-365">Linux dağıtımları için, `export` oturum tabanlı değişken ayarları ve makine düzeyi ortam ayarları için *bash_profile* dosyası için komut isteminde komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="455af-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="455af-366">Kodda ortam ayarlama</span><span class="sxs-lookup"><span data-stu-id="455af-366">Set the environment in code</span></span>

<span data-ttu-id="455af-367"><xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>Ana bilgisayarı derlerken çağırın.</span><span class="sxs-lookup"><span data-stu-id="455af-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="455af-368">Bkz. <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="455af-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="455af-369">Ortama göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="455af-369">Configuration by environment</span></span>

<span data-ttu-id="455af-370">Yapılandırmayı ortama göre yüklemek için şunları yapmanızı öneririz:</span><span class="sxs-lookup"><span data-stu-id="455af-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="455af-371">*appSettings* dosyaları (*appSettings. { Environment}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="455af-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="455af-372">Bkz. <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="455af-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="455af-373">Ortam değişkenleri (uygulamanın barındırıldığı her bir sistemde ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="455af-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="455af-374">Bkz <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables> . ve.</span><span class="sxs-lookup"><span data-stu-id="455af-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="455af-375">Gizli dizi Yöneticisi (yalnızca geliştirme ortamında).</span><span class="sxs-lookup"><span data-stu-id="455af-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="455af-376">Bkz. <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="455af-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="455af-377">Ortam tabanlı başlangıç sınıfı ve yöntemleri</span><span class="sxs-lookup"><span data-stu-id="455af-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="455af-378">Startup.Configure 'a ıhostingenvironment ekleme</span><span class="sxs-lookup"><span data-stu-id="455af-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="455af-379"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>İçine Ekle `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="455af-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="455af-380">Bu yaklaşım, uygulama yalnızca `Startup.Configure` , ortam başına en az kod farklılığı olan birkaç ortam için yapılandırma gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="455af-381">Başlangıç sınıfına ıhostingenvironment ekleme</span><span class="sxs-lookup"><span data-stu-id="455af-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="455af-382"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> `Startup` Oluşturucuya ekleyin ve sınıfın tamamında kullanmak üzere bir alana hizmeti atayın `Startup` .</span><span class="sxs-lookup"><span data-stu-id="455af-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="455af-383">Bu yaklaşım, uygulama her ortam için en az kod farklılığı olan birkaç ortam için başlatma yapılandırması gerektirdiğinde faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="455af-384">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="455af-384">In the following example:</span></span>

* <span data-ttu-id="455af-385">Ortam `_env` alanında tutulur.</span><span class="sxs-lookup"><span data-stu-id="455af-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="455af-386">`_env`, ve ' de, `ConfigureServices` `Configure` uygulamanın ortamına göre başlangıç yapılandırması uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="455af-387">Başlangıç sınıfı kuralları</span><span class="sxs-lookup"><span data-stu-id="455af-387">Startup class conventions</span></span>

<span data-ttu-id="455af-388">ASP.NET Core bir uygulama başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyükleme.</span><span class="sxs-lookup"><span data-stu-id="455af-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="455af-389">Uygulama `Startup` farklı ortamlar için ayrı sınıflar tanımlayabilir (örneğin, `StartupDevelopment` ).</span><span class="sxs-lookup"><span data-stu-id="455af-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="455af-390">Çalışma zamanında uygun `Startup` sınıf seçildi.</span><span class="sxs-lookup"><span data-stu-id="455af-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="455af-391">Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir.</span><span class="sxs-lookup"><span data-stu-id="455af-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="455af-392">Eşleşen bir `Startup{EnvironmentName}` sınıf bulunamazsa `Startup` sınıf kullanılır.</span><span class="sxs-lookup"><span data-stu-id="455af-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="455af-393">Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="455af-394">Ortam tabanlı sınıfları uygulamak için `Startup` , `Startup{EnvironmentName}` Kullanımdaki her ortam için bir sınıf ve bir geri dönüş sınıfı oluşturun `Startup` :</span><span class="sxs-lookup"><span data-stu-id="455af-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="455af-395">Derleme adını kabul eden [Usestartup (ıwebhostbuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="455af-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="455af-396">Başlangıç yöntemi kuralları</span><span class="sxs-lookup"><span data-stu-id="455af-396">Startup method conventions</span></span>

<span data-ttu-id="455af-397">[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , formun ve ortama özgü sürümlerini destekler `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="455af-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="455af-398">Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="455af-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="455af-399">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="455af-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
