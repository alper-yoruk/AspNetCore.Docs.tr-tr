---
title: ASP.NET Core Modülü
author: rick-anderson
description: ASP.NET Core uygulamalarını barındırmak için ASP.NET Çekirdek Modül'ASP.NET nasıl yapılandırılabildiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667302"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="c966d-103">ASP.NET Core Modülü</span><span class="sxs-lookup"><span data-stu-id="c966d-103">ASP.NET Core Module</span></span>

<span data-ttu-id="c966d-104">Tom [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), Rick [Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti)ve [Justin Kotalik](https://github.com/jkotalik) tarafından</span><span class="sxs-lookup"><span data-stu-id="c966d-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c966d-105">ASP.NET Çekirdek Modülü, IIS boru hattına aşağıdakilerden herhangi birini takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="c966d-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="c966d-106">IIS işçi sürecinin içinde bir ASP.NET`w3wp.exe`Core uygulaması barındırın ( ), [süreç içi barındırma modeli](#in-process-hosting-model)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="c966d-107">[Kestrel sunucusu](xref:fundamentals/servers/kestrel)nu çalıştıran bir arka uç ASP.NET Core uygulamasına web isteklerini [iletin, işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="c966d-108">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="c966d-108">Supported Windows versions:</span></span>

* <span data-ttu-id="c966d-109">Windows 7 veya sonraki</span><span class="sxs-lookup"><span data-stu-id="c966d-109">Windows 7 or later</span></span>
* <span data-ttu-id="c966d-110">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="c966d-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c966d-111">Süreç içinde barındırma yaparken, modül IIS için IIS HTTP Server (`IISHttpServer`) adı verilen bir işlem içi sunucu uygulaması kullanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="c966d-112">Proses dışı ev sahipliği yaparken, modül sadece Kerkenez ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="c966d-113">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="c966d-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c966d-114">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="c966d-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c966d-115">Süreç içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="c966d-115">In-process hosting model</span></span>

<span data-ttu-id="c966d-116">ASP.NET Core uygulamaları, işlem içi barındırma modeline varsayılan olarak.</span><span class="sxs-lookup"><span data-stu-id="c966d-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="c966d-117">Süreç içinde barındırma yaparken aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="c966d-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="c966d-118">IIS HTTP`IISHttpServer`Server ( ) [Kerkenez](xref:fundamentals/servers/kestrel) sunucusu yerine kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="c966d-119">Süreç içinde [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) şu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> çağrıları ile çağırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="c966d-120">Kaydedin. `IISHttpServer`</span><span class="sxs-lookup"><span data-stu-id="c966d-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="c966d-121">ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="c966d-122">Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="c966d-123">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="c966d-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="c966d-124">Uygulamalar arasında uygulama havuzu paylaşmak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="c966d-125">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-125">Use one app pool per app.</span></span>

* <span data-ttu-id="c966d-126">Web [Dağıtımı'nı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya [dağıtıma bir app_offline.htm dosyasını](xref:host-and-deploy/iis/index#locked-deployment-files)el ile yerleştirirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="c966d-127">Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="c966d-128">Uygulamanın mimarisi (bitness) ve yüklü çalışma süresi (x64 veya x86) uygulama havuzunun mimarisi yle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="c966d-129">İstemci bağlantıları algılanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-129">Client disconnects are detected.</span></span> <span data-ttu-id="c966d-130">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci, istemci bağlantısı kesildiğinde iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="c966d-131">Core 2.2.1 veya daha <xref:System.IO.Directory.GetCurrentDirectory*> önceki ASP.NET, uygulamanın dizini yerine IIS tarafından başlatılan işlemin alt dizini döndürür (örneğin, *w3wp.exe*için *C:\Windows\System32\inetsrv).*</span><span class="sxs-lookup"><span data-stu-id="c966d-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="c966d-132">Uygulamanın geçerli dizinini ayarlayan örnek kod için [CurrentDirectoryHelpers sınıfına](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="c966d-133">`SetCurrentDirectory` Yöntemi ara.</span><span class="sxs-lookup"><span data-stu-id="c966d-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="c966d-134">Uygulamanın <xref:System.IO.Directory.GetCurrentDirectory*> dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="c966d-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="c966d-135">Süreç içinde barındırma <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> yaparken, bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="c966d-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="c966d-136">Bu nedenle, her kimlik doğrulaması varsayılan olarak etkinleştirilmedikten sonra talepleri dönüştürmek için kullanılan bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulama.</span><span class="sxs-lookup"><span data-stu-id="c966d-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="c966d-137">Bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla talepleri dönüştürürken, kimlik doğrulama hizmetleri eklemeyi çağırın: <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*></span><span class="sxs-lookup"><span data-stu-id="c966d-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="c966d-138">[Web Paketi (tek dosyalı) dağıtımlar](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c966d-139">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="c966d-139">Out-of-process hosting model</span></span>

<span data-ttu-id="c966d-140">İşlem dışı barındırma için bir uygulamayı yapılandırmak için, `<AspNetCoreHostingModel>` özelliğin `OutOfProcess` değerini proje dosyasındaki *(.csproj)* ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c966d-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="c966d-141">Süreç içi `InProcess`barındırma, varsayılan değer olan ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="c966d-142">Değeri büyük/küçük harf `<AspNetCoreHostingModel>` duyarsız, bu yüzden `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="c966d-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="c966d-143">[Kerkenez](xref:fundamentals/servers/kestrel) sunucusu Yerine IIS HTTP`IISHttpServer`Server ( ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="c966d-144">İşlem dışı için [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) şunları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> çağırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="c966d-145">ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="c966d-146">Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="c966d-147">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="c966d-147">Hosting model changes</span></span>

<span data-ttu-id="c966d-148">Ayarı `hostingModel` *web.config* dosyasında değiştirilirse [(web.config](#configuration-with-webconfig) bölümü ile Yapılandırma'da açıklanırsa), modül IIS için alt işlemi geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="c966d-149">IIS Express için modül, alt işlemi geri dönüştürmez, bunun yerine geçerli IIS Express işleminin zarif bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="c966d-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="c966d-150">Uygulamanın bir sonraki isteği yeni bir IIS Express işlemi doğurur.</span><span class="sxs-lookup"><span data-stu-id="c966d-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="c966d-151">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="c966d-151">Process name</span></span>

<span data-ttu-id="c966d-152">`Process.GetCurrentProcess().ProcessName``w3wp` / raporlar `iisexpress` (süreç içi) `dotnet` veya (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="c966d-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="c966d-153">Windows Kimlik Doğrulama gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="c966d-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="c966d-154">ASP.NET Çekirdek Modülü ile etkin olan IIS modülleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/iis/modules></span><span class="sxs-lookup"><span data-stu-id="c966d-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="c966d-155">ASP.NET Çekirdek Modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="c966d-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="c966d-156">Alt işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="c966d-157">Sorun giderme başlangıç sorunları için dosya depolamasına stdout çıktısını günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c966d-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="c966d-158">İleri Windows kimlik doğrulama belirteçleri.</span><span class="sxs-lookup"><span data-stu-id="c966d-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="c966d-159">ASP.NET Çekirdek Modülü nasıl yüklenir ve kullanılır?</span><span class="sxs-lookup"><span data-stu-id="c966d-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="c966d-160">ASP.NET Çekirdek Modülü nasıl yüklenir yönergeleri [için](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)bkz.</span><span class="sxs-lookup"><span data-stu-id="c966d-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="c966d-161">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c966d-161">Configuration with web.config</span></span>

<span data-ttu-id="c966d-162">ASP.NET Çekirdek Modülü, sitenin `aspNetCore` `system.webServer` *web.config* dosyasındaki düğüm bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="c966d-163">Aşağıdaki *web.config* [dosyası, çerçeveye bağımlı](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) bir dağıtım için yayımlanır ve site isteklerini işlemek için ASP.NET Çekirdek Modül'u yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="c966d-164">Aşağıdaki *web.config* [kendi kendine yeten bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayınlanır:</span><span class="sxs-lookup"><span data-stu-id="c966d-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="c966d-165">Özellik, <xref:System.Configuration.SectionInformation.InheritInChildApplications*> konumu `false` [ \<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınılolmadığını belirtmek üzere ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c966d-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="c966d-166">Bir uygulama [Azure Uygulama Hizmeti'ne](https://azure.microsoft.com/services/app-service/) `stdoutLogFile` dağıtıldığında, `\\?\%home%\LogFiles\stdout`yol .</span><span class="sxs-lookup"><span data-stu-id="c966d-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c966d-167">Yol, hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne stdout günlüklerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c966d-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="c966d-168">IIS alt uygulama yapılandırması hakkında <xref:host-and-deploy/iis/index#sub-applications>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c966d-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="c966d-169">aspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="c966d-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="c966d-170">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="c966d-170">Attribute</span></span> | <span data-ttu-id="c966d-171">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c966d-171">Description</span></span> | <span data-ttu-id="c966d-172">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="c966d-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="c966d-173">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-173">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-174">**ProcessPath'de**belirtilen yürütülebilir bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="c966d-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="c966d-175">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-176">Doğruysa, **502.5 - İşlem Hatası** sayfası bastırılır ve *web.config'de* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="c966d-177">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-178">Doğruysa, belirteç istek başına 'MS-ASPNETCORE-WINAUTHTOKEN' üstbilgi olarak %ASPNETCORE_PORT'da dinleyerek alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="c966d-179">İstek başına bu belirteç için CloseHandle'ı aramak bu işlemin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="c966d-180">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-180">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-181">Barındırma modelini süreç içi (`InProcess`/`inprocess`) veya süreç dışı`OutOfProcess`/`outofprocess`olarak belirtir ( ).</span><span class="sxs-lookup"><span data-stu-id="c966d-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="c966d-182">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-183">Uygulama başına döndürülebilen **processPath** ayarında belirtilen işlemin örnek sayısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="c966d-184">&dagger;Süreç içi barındırma için, değer `1`.</span><span class="sxs-lookup"><span data-stu-id="c966d-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="c966d-185">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="c966d-186">Bu öznitelik gelecekteki bir sürümde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="c966d-187">Varsayılan:`1`</span><span class="sxs-lookup"><span data-stu-id="c966d-187">Default: `1`</span></span><br><span data-ttu-id="c966d-188">Dk:`1`</span><span class="sxs-lookup"><span data-stu-id="c966d-188">Min: `1`</span></span><br><span data-ttu-id="c966d-189">Max:`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="c966d-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="c966d-190">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-190">Required string attribute.</span></span></p><p><span data-ttu-id="c966d-191">HTTP isteklerini dinleyerek bir işlem başlatan yürütülebilir yol.</span><span class="sxs-lookup"><span data-stu-id="c966d-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="c966d-192">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-192">Relative paths are supported.</span></span> <span data-ttu-id="c966d-193">Yol ile `.`başlarsa, yol site köküne göre olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="c966d-194">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-195">**ProcessPath'de** belirtilen işlemin dakikada çökmesine izin verilme sayısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="c966d-196">Bu sınır aşılırsa, modül dakikanın geri kalanı için işlemi başlatmayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="c966d-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="c966d-197">Süreç içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="c966d-198">Varsayılan:`10`</span><span class="sxs-lookup"><span data-stu-id="c966d-198">Default: `10`</span></span><br><span data-ttu-id="c966d-199">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-199">Min: `0`</span></span><br><span data-ttu-id="c966d-200">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="c966d-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="c966d-201">İsteğe bağlı zaman alanı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="c966d-202">ASP.NET Çekirdek Modülü'nün %ASPNETCORE_PORT'de dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="c966d-203">ASP.NET Core 2.1 veya daha sonra sürümü ile gönderilen ASP.NET `requestTimeout` Çekirdek Modülü sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="c966d-204">Süreç içi barındırma için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="c966d-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="c966d-205">Süreç içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="c966d-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="c966d-206">Dize dakika ve saniye segmentleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="c966d-207">60 **60** değerinin dakika veya saniye olarak kullanılması *500 ile*sonuçlanır - Internal Server Hatası.</span><span class="sxs-lookup"><span data-stu-id="c966d-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="c966d-208">Varsayılan:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-208">Default: `00:02:00`</span></span><br><span data-ttu-id="c966d-209">Dk:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-209">Min: `00:00:00`</span></span><br><span data-ttu-id="c966d-210">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="c966d-211">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-212">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir inin düzgün bir şekilde kapanmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="c966d-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="c966d-213">Varsayılan:`10`</span><span class="sxs-lookup"><span data-stu-id="c966d-213">Default: `10`</span></span><br><span data-ttu-id="c966d-214">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-214">Min: `0`</span></span><br><span data-ttu-id="c966d-215">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="c966d-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="c966d-216">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-217">Modülün çalıştırılabilenin bağlantı noktasını dinleyerek bir işlem başlatmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="c966d-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="c966d-218">Bu zaman sınırı aşılırsa, modül işlemi öldürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="c966d-219">Modül, yeni bir istek aldığında işlemi yeniden başlatmaya çalışır ve uygulama son dakika içinde hızlı bir **şekilde FailsPerMinute** sayısını başlatamazsa sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="c966d-220">0 (sıfır) değeri sonsuz zaman kaybı olarak kabul **edilmez.**</span><span class="sxs-lookup"><span data-stu-id="c966d-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="c966d-221">Varsayılan:`120`</span><span class="sxs-lookup"><span data-stu-id="c966d-221">Default: `120`</span></span><br><span data-ttu-id="c966d-222">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-222">Min: `0`</span></span><br><span data-ttu-id="c966d-223">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="c966d-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="c966d-224">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-225">Doğruysa, **processPath** belirtilen işlem için **stdout** ve **stderr** **stdoutLogFile**belirtilen dosyaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="c966d-226">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-226">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-227">**ProcessPath'de** belirtilen işlemden **stdout** ve **stderr** günlüğe kaydedilmiş olan göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="c966d-228">Göreli yollar sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="c966d-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="c966d-229">Herhangi bir `.` yol ile başlayan site köküne göre ve diğer tüm yollar mutlak yollar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="c966d-230">Yol dosyasında sağlanan tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="c966d-231">Alt çizgi delimiters kullanarak, bir zaman damgası, işlem kimliği ve dosya uzantısı (*.log*) **stdoutLogFile** yolunun son bölümüne eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="c966d-232">Değer `.\logs\stdout` olarak veseği, 2/5/2018 tarihinde saat 19:41:32'de 1934 işlem kimliğiyle kaydedilirken, örneğin stdout günlüğü *stdout_20180205194132_1934.log* olarak kaydedilir. *logs*</span><span class="sxs-lookup"><span data-stu-id="c966d-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="c966d-233">Ortam değişkenlerini belirleme</span><span class="sxs-lookup"><span data-stu-id="c966d-233">Set environment variables</span></span>

<span data-ttu-id="c966d-234">Öznitelikteki `processPath` işlem için ortam değişkenleri belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="c966d-235">Bir koleksiyon öğesinin `<environmentVariable>` alt öğesi ile bir ortam değişkeni belirtin. `<environmentVariables>`</span><span class="sxs-lookup"><span data-stu-id="c966d-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="c966d-236">Bu bölümde ayarlanan ortam değişkenleri sistem ortamı değişkenlerinden önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="c966d-237">Aşağıdaki örnek, *web.config'de*iki ortam değişkeni belirler. `ASPNETCORE_ENVIRONMENT` uygulamanın ortamını `Development`.</span><span class="sxs-lookup"><span data-stu-id="c966d-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="c966d-238">Geliştirici, geliştirici [özel durum sayfasını](xref:fundamentals/error-handling) hata ayıklarken Geliştirici Özel Durum Sayfasını yüklemeye zorlamak için bu değeri *web.config* dosyasında geçici olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="c966d-239">`CONFIG_DIR`geliştirici, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak için başlangıç değerini okuyan bir kod yazdığı kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="c966d-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="c966d-240">Ortamı doğrudan *web.config'de* ayarlamanın alternatifi, `<EnvironmentName>` özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına eklemektir.</span><span class="sxs-lookup"><span data-stu-id="c966d-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="c966d-241">Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="c966d-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="c966d-242">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` `Development` yalnızca Internet gibi güvenilmeyen ağlar tarafından erişilemeyen hazırlama ve sınama sunucularına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="c966d-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="c966d-243">app_offline.htm</span></span>

<span data-ttu-id="c966d-244">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="c966d-245">Uygulama tanımlanan saniye sayısından sonra çalışmaya `shutdownTimeLimit`devam ediyorsa, ASP.NET Çekirdek Modülü çalışma işlemini öldürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="c966d-246">*app_offline.htm* dosyası mevcut ken, ASP.NET Çekirdek Modülü *app_offline.htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="c966d-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="c966d-247">*app_offline.htm* dosyası kaldırıldığında, bir sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="c966d-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="c966d-248">İşlem dışı barındırma modelini kullanırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="c966d-249">Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="c966d-250">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="c966d-250">Start-up error page</span></span>

<span data-ttu-id="c966d-251">Hem süreç içi hem de süreç dışı barındırma, uygulamayı başlatamadıkları zaman özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="c966d-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="c966d-252">ASP.NET Çekirdek Modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500.0 - İşlem İçi/İşlem Dışı İşleyici Yük Hatası* kod sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="c966d-253">ASP.NET Çekirdek Modülü uygulamayı başlatamazsa, işlem içi barındırma için *500.30 - Start Failure* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="c966d-254">ASP.NET Çekirdek Modülü arka uç işlemini başlatamazsa veya arka uç işlemi başlarsa ancak yapılandırılan bağlantı noktasını dinlemezse, *502.5 - İşlem Hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="c966d-255">Bu sayfayı bastırmak ve varsayılan IIS 5xx durum kodu `disableStartUpErrorPage` sayfasına geri dönmek için özniteliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="c966d-256">Özel hata iletilerinin yapılandırılması hakkında daha fazla bilgi için [HTTP Hataları \<httpErrors>. ](/iis/configuration/system.webServer/httpErrors/)</span><span class="sxs-lookup"><span data-stu-id="c966d-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="c966d-257">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="c966d-257">Log creation and redirection</span></span>

<span data-ttu-id="c966d-258">ASP.NET Çekirdek Modülü, `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` öğenin öznitelikleri ve öznitelikleri ayarlanmışsa, stdout ve stderr konsol çıktısını diske yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="c966d-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="c966d-259">`stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="c966d-260">Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="c966d-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="c966d-261">İşlem geri dönüşümü/yeniden başlatma gerçekleşmedikçe günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="c966d-262">Günlüklerin tükettiği disk alanını sınırlamak ana bilgisayarın sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="c966d-263">Stdout günlüğünün kullanılması yalnızca IIS'de barındırma yaparken veya [Visual Studio ile IIS için geliştirme zamanı desteğini](xref:host-and-deploy/iis/development-time-iis-support)kullanırken sorun giderme uygulaması başlatma sorunları için önerilir, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken değil.</span><span class="sxs-lookup"><span data-stu-id="c966d-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="c966d-264">Stdout günlüğünü genel uygulama günlüğü amacıyla kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="c966d-265">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c966d-266">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="c966d-267">Günlük dosyası oluşturulduğunda otomatik olarak bir zaman damgası ve dosya uzantısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="c966d-268">Günlük dosya adı, `stdoutLogFile` zaman damgası, işlem kimliği ve dosya uzantısı *(.log*) yolun son kesimine (genellikle *stdout)* alt çizgiyle sınırlandırılmış olarak eklenerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="c966d-269">`stdoutLogFile` Yol *stdout*ile biterse , 2/5/2018 tarihinde 19:42:32 tarihinde oluşturulan 1934 PID ile bir uygulama için bir günlük *stdout_20180205194132_1934.log*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="c966d-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="c966d-270">Yanlışsa, `stdoutLogEnabled` uygulama başlatmada oluşan hatalar yakalanır ve 30 KB'a kadar olay günlüğüne yayılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="c966d-271">Başlatmadan sonra, tüm ek günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="c966d-272">Aşağıdaki örnek `aspNetCore` öğe, nispi yolda `.\log\`stdout günlüğe yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c966d-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="c966d-273">AppPool kullanıcı kimliğinin sağlanan yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="c966d-274">Azure Uygulama Hizmeti dağıtımı için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değerini `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="c966d-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c966d-275">Ortam `%home` değişkeni, Azure Uygulama Hizmeti tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c966d-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="c966d-276">Günlük filtresi kuralları oluşturmak için, ASP.NET Core günlük belgelerinin [Yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [Günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="c966d-277">Yol biçimleri hakkında daha fazla bilgi için [Windows sistemlerinde Dosya yolu biçimlerine](/dotnet/standard/io/file-path-formats)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="c966d-278">Geliştirilmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="c966d-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="c966d-279">ASP.NET Çekirdek Modülü gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="c966d-280">Öğeyi `<handlerSettings>` `<aspNetCore>` *web.config'deki*öğeye ekleyin. Tanılama `debugLevel` `TRACE` bilgilerinin daha yüksek bir sadakatini ortaya çıkaracak şekilde ayarlanın:</span><span class="sxs-lookup"><span data-stu-id="c966d-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="c966d-281">Yoldaki tüm klasörler (önceki örnekte*günlükler)* günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="c966d-282">Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="c966d-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="c966d-283">Hata ayıklama`debugLevel`düzeyi ( ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="c966d-284">Düzeyler (en azdan en ayrıntılısıraya kadar):</span><span class="sxs-lookup"><span data-stu-id="c966d-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="c966d-285">HATA</span><span class="sxs-lookup"><span data-stu-id="c966d-285">ERROR</span></span>
* <span data-ttu-id="c966d-286">UYARI</span><span class="sxs-lookup"><span data-stu-id="c966d-286">WARNING</span></span>
* <span data-ttu-id="c966d-287">Bilgi</span><span class="sxs-lookup"><span data-stu-id="c966d-287">INFO</span></span>
* <span data-ttu-id="c966d-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="c966d-288">TRACE</span></span>

<span data-ttu-id="c966d-289">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="c966d-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="c966d-290">Konsol</span><span class="sxs-lookup"><span data-stu-id="c966d-290">CONSOLE</span></span>
* <span data-ttu-id="c966d-291">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c966d-291">EVENTLOG</span></span>
* <span data-ttu-id="c966d-292">DOSYA</span><span class="sxs-lookup"><span data-stu-id="c966d-292">FILE</span></span>

<span data-ttu-id="c966d-293">İşleyici ayarları ortam değişkenleri aracılığıyla da sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="c966d-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="c966d-294">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Hata ayıklama günlüğü dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="c966d-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="c966d-295">(Varsayılan: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="c966d-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="c966d-296">`ASPNETCORE_MODULE_DEBUG`&ndash; Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="c966d-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="c966d-297">Hata **not** ayıklama günlüğekaydetmeyi dağıtımda bir sorunu gidermek için gerekenden daha uzun süre etkin bırakmayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="c966d-298">Günlüğün boyutu sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="c966d-298">The size of the log isn't limited.</span></span> <span data-ttu-id="c966d-299">Hata ayıklama günlüğünü etkin bırakmak, kullanılabilir disk alanını tüketebilir ve sunucuyu veya uygulama hizmetini kilitleyebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="c966d-300">*Web.config* dosyasındaki `aspNetCore` öğenin bir örneği için [web.config ile Yapılandırma'ya](#configuration-with-webconfig) bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="c966d-301">Yığın boyutunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="c966d-301">Modify the stack size</span></span>

<span data-ttu-id="c966d-302">*Yalnızca işlem içi barındırma modelini kullanırken geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c966d-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="c966d-303">yönetilen yığın boyutunu `stackSize` *web.config'deki*baytayarını kullanarak yapılandırın. Varsayılan boyut `1048576` bayt (1 MB) 'dir.</span><span class="sxs-lookup"><span data-stu-id="c966d-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="c966d-304">Proxy yapılandırması HTTP protokolü ve eşleştirme belirteci kullanır</span><span class="sxs-lookup"><span data-stu-id="c966d-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="c966d-305">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c966d-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="c966d-306">ASP.NET Çekirdek Modülü ile Kestrel arasında oluşturulan proxy HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="c966d-307">Modül ve Kestrel arasındaki trafiği sunucunun dışında bir konumdan gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="c966d-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="c966d-308">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından yerine getirilmiş olduğunu ve başka bir kaynaktan gelmediğini garanti etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="c966d-309">Eşleştirme belirteci modül tarafından oluşturulur ve`ASPNETCORE_TOKEN`bir ortam değişkeni () olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="c966d-310">Eşleştirme belirteci, her yakın istekte`MS-ASPNETCORE-TOKEN`bir üstbilgi () olarak da ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="c966d-311">IIS Middleware, eşleştirme belirteç üstbilgi değerinin ortam değişken değeriyle eşleştiğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="c966d-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="c966d-312">Belirteç değerleri eşleşmezse, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="c966d-313">Eşleştirme belirteç ortamı değişkeni ve modül ile Kestrel arasındaki trafiğe sunucunun dışında bir konumdan erişilemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="c966d-314">Eşleştirme belirteç değerini bilmeden, saldırgan IIS Middleware'de denetimi atlayan istekler gönderemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="c966d-315">IIS Paylaşılan Yapılandırmalı ASP.NET Çekirdek Modülü</span><span class="sxs-lookup"><span data-stu-id="c966d-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="c966d-316">ASP.NET Çekirdek Modülü yükleyici, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="c966d-317">Yerel sistem hesabı, IIS Paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değişiklik izni olmadığından, yükleci, paylaşımdaki *applicationHost.config* dosyasındaki modül ayarlarını yapılandırmaya çalışırken erişim reddedilen bir hata atar.</span><span class="sxs-lookup"><span data-stu-id="c966d-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="c966d-318">IIS yüklemesi ile aynı makinede bir IIS Paylaşılan Yapılandırma kullanırken, `OPT_NO_SHARED_CONFIG_CHECK` parametre ayarlanmış `1`ASP.NET Core Hosting Bundle yükleyicisini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c966d-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="c966d-319">Paylaşılan yapılandırmaya giden yol IIS yüklemeyle aynı makinede değilse aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="c966d-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="c966d-320">IIS Paylaşılan Yapılandırmasını devre dışı kınla.</span><span class="sxs-lookup"><span data-stu-id="c966d-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="c966d-321">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-321">Run the installer.</span></span>
1. <span data-ttu-id="c966d-322">Paylaşıma güncelleştirilmiş *applicationHost.config* dosyasını dışa aktarın.</span><span class="sxs-lookup"><span data-stu-id="c966d-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="c966d-323">IIS Paylaşılan Yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c966d-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="c966d-324">Modül sürümü ve Hosting Paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="c966d-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="c966d-325">Yüklü ASP.NET Çekirdek Modülü sürümünü belirlemek için:</span><span class="sxs-lookup"><span data-stu-id="c966d-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="c966d-326">Barındırma sisteminde *% windir%\System32\inetsrv'e*gidin.</span><span class="sxs-lookup"><span data-stu-id="c966d-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="c966d-327">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="c966d-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="c966d-328">Dosyaya sağ tıklayın ve bağlamsal menüden **Özellikler'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="c966d-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="c966d-329">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve Ürün **sürümü** modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="c966d-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="c966d-330">Modül için Hosting Paketi yükleyici günlükleri *C\\\\bulunur: Kullanıcılar\\%UserName%\\AppData Local\\Temp*. Dosya zaman *damgası>_000_AspNetCoreModule_x64.log dd_DotNetCoreWinSvrHosting__\<* adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="c966d-331">Modül, şema ve yapılandırma dosya konumları</span><span class="sxs-lookup"><span data-stu-id="c966d-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="c966d-332">Modül</span><span class="sxs-lookup"><span data-stu-id="c966d-332">Module</span></span>

<span data-ttu-id="c966d-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c966d-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="c966d-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-336">%ProgramFiles%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="c966d-337">%ProgramFiles(x86)%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="c966d-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c966d-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="c966d-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-341">%ProgramFiles%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="c966d-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="c966d-343">Şema</span><span class="sxs-lookup"><span data-stu-id="c966d-343">Schema</span></span>

<span data-ttu-id="c966d-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c966d-344">**IIS**</span></span>

* <span data-ttu-id="c966d-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="c966d-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="c966d-347">**IIS Ekspresi**</span><span class="sxs-lookup"><span data-stu-id="c966d-347">**IIS Express**</span></span>

* <span data-ttu-id="c966d-348">%ProgramFiles%\IIS Express\config\şema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="c966d-349">%ProgramFiles%\IIS Express\config\şema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="c966d-350">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c966d-350">Configuration</span></span>

<span data-ttu-id="c966d-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c966d-351">**IIS**</span></span>

* <span data-ttu-id="c966d-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="c966d-353">**IIS Ekspresi**</span><span class="sxs-lookup"><span data-stu-id="c966d-353">**IIS Express**</span></span>

* <span data-ttu-id="c966d-354">Görsel Stüdyo: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="c966d-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="c966d-356">Dosyalar *applicationHost.config* dosyasında *aspnetcore* arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c966d-357">ASP.NET Çekirdek Modülü, IIS boru hattına aşağıdakilerden herhangi birini takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="c966d-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="c966d-358">IIS işçi sürecinin içinde bir ASP.NET`w3wp.exe`Core uygulaması barındırın ( ), [süreç içi barındırma modeli](#in-process-hosting-model)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="c966d-359">[Kestrel sunucusu](xref:fundamentals/servers/kestrel)nu çalıştıran bir arka uç ASP.NET Core uygulamasına web isteklerini [iletin, işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="c966d-360">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="c966d-360">Supported Windows versions:</span></span>

* <span data-ttu-id="c966d-361">Windows 7 veya sonraki</span><span class="sxs-lookup"><span data-stu-id="c966d-361">Windows 7 or later</span></span>
* <span data-ttu-id="c966d-362">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="c966d-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c966d-363">Süreç içinde barındırma yaparken, modül IIS için IIS HTTP Server (`IISHttpServer`) adı verilen bir işlem içi sunucu uygulaması kullanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="c966d-364">Proses dışı ev sahipliği yaparken, modül sadece Kerkenez ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="c966d-365">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="c966d-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c966d-366">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="c966d-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c966d-367">Süreç içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="c966d-367">In-process hosting model</span></span>

<span data-ttu-id="c966d-368">İşlem içi barındırma için bir uygulamayı yapılandırmak için, özelliği uygulamanın `<AspNetCoreHostingModel>` proje `InProcess` dosyasına bir değerle ekleyin `OutOfProcess`(işlem dışı barındırma ile ayarlanır):</span><span class="sxs-lookup"><span data-stu-id="c966d-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="c966d-369">İşlem içi barındırma modeli,.NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="c966d-370">Değeri büyük/küçük harf `<AspNetCoreHostingModel>` duyarsız, bu yüzden `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="c966d-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="c966d-371">`<AspNetCoreHostingModel>` Özellik dosyada yoksa, varsayılan `OutOfProcess`değer.</span><span class="sxs-lookup"><span data-stu-id="c966d-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="c966d-372">Süreç içinde barındırma yaparken aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="c966d-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="c966d-373">IIS HTTP`IISHttpServer`Server ( ) [Kerkenez](xref:fundamentals/servers/kestrel) sunucusu yerine kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="c966d-374">Süreç içinde [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) şu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> çağrıları ile çağırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="c966d-375">Kaydedin. `IISHttpServer`</span><span class="sxs-lookup"><span data-stu-id="c966d-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="c966d-376">ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="c966d-377">Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="c966d-378">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="c966d-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="c966d-379">Uygulamalar arasında uygulama havuzu paylaşmak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="c966d-380">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-380">Use one app pool per app.</span></span>

* <span data-ttu-id="c966d-381">Web [Dağıtımı'nı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya [dağıtıma bir app_offline.htm dosyasını](xref:host-and-deploy/iis/index#locked-deployment-files)el ile yerleştirirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="c966d-382">Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="c966d-383">Uygulamanın mimarisi (bitness) ve yüklü çalışma süresi (x64 veya x86) uygulama havuzunun mimarisi yle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="c966d-384">İstemci bağlantıları algılanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-384">Client disconnects are detected.</span></span> <span data-ttu-id="c966d-385">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci, istemci bağlantısı kesildiğinde iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="c966d-386">Core 2.2.1 veya daha <xref:System.IO.Directory.GetCurrentDirectory*> önceki ASP.NET, uygulamanın dizini yerine IIS tarafından başlatılan işlemin alt dizini döndürür (örneğin, *w3wp.exe*için *C:\Windows\System32\inetsrv).*</span><span class="sxs-lookup"><span data-stu-id="c966d-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="c966d-387">Uygulamanın geçerli dizinini ayarlayan örnek kod için [CurrentDirectoryHelpers sınıfına](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="c966d-388">`SetCurrentDirectory` Yöntemi ara.</span><span class="sxs-lookup"><span data-stu-id="c966d-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="c966d-389">Uygulamanın <xref:System.IO.Directory.GetCurrentDirectory*> dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="c966d-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="c966d-390">Süreç içinde barındırma <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> yaparken, bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="c966d-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="c966d-391">Bu nedenle, her kimlik doğrulaması varsayılan olarak etkinleştirilmedikten sonra talepleri dönüştürmek için kullanılan bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulama.</span><span class="sxs-lookup"><span data-stu-id="c966d-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="c966d-392">Bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla talepleri dönüştürürken, kimlik doğrulama hizmetleri eklemeyi çağırın: <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*></span><span class="sxs-lookup"><span data-stu-id="c966d-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c966d-393">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="c966d-393">Out-of-process hosting model</span></span>

<span data-ttu-id="c966d-394">İşlem dışı barındırma için bir uygulamayı yapılandırmak için proje dosyasındaki aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="c966d-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="c966d-395">Özelliği belirtmeyin. `<AspNetCoreHostingModel>`</span><span class="sxs-lookup"><span data-stu-id="c966d-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="c966d-396">`<AspNetCoreHostingModel>` Özellik dosyada yoksa, varsayılan `OutOfProcess`değer.</span><span class="sxs-lookup"><span data-stu-id="c966d-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="c966d-397">Özelliğin `<AspNetCoreHostingModel>` değerini `OutOfProcess` (işlem içi barındırma ile `InProcess`ayarlanır) ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c966d-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="c966d-398">Değer büyük/küçük harf `inprocess` duyarsız, bu nedenle ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="c966d-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="c966d-399">[Kerkenez](xref:fundamentals/servers/kestrel) sunucusu Yerine IIS HTTP`IISHttpServer`Server ( ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="c966d-400">İşlem dışı için [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) şunları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> çağırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="c966d-401">ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="c966d-402">Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="c966d-403">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="c966d-403">Hosting model changes</span></span>

<span data-ttu-id="c966d-404">Ayarı `hostingModel` *web.config* dosyasında değiştirilirse [(web.config](#configuration-with-webconfig) bölümü ile Yapılandırma'da açıklanırsa), modül IIS için alt işlemi geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="c966d-405">IIS Express için modül, alt işlemi geri dönüştürmez, bunun yerine geçerli IIS Express işleminin zarif bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="c966d-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="c966d-406">Uygulamanın bir sonraki isteği yeni bir IIS Express işlemi doğurur.</span><span class="sxs-lookup"><span data-stu-id="c966d-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="c966d-407">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="c966d-407">Process name</span></span>

<span data-ttu-id="c966d-408">`Process.GetCurrentProcess().ProcessName``w3wp` / raporlar `iisexpress` (süreç içi) `dotnet` veya (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="c966d-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="c966d-409">Windows Kimlik Doğrulama gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="c966d-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="c966d-410">ASP.NET Çekirdek Modülü ile etkin olan IIS modülleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/iis/modules></span><span class="sxs-lookup"><span data-stu-id="c966d-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="c966d-411">ASP.NET Çekirdek Modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="c966d-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="c966d-412">Alt işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="c966d-413">Sorun giderme başlangıç sorunları için dosya depolamasına stdout çıktısını günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c966d-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="c966d-414">İleri Windows kimlik doğrulama belirteçleri.</span><span class="sxs-lookup"><span data-stu-id="c966d-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="c966d-415">ASP.NET Çekirdek Modülü nasıl yüklenir ve kullanılır?</span><span class="sxs-lookup"><span data-stu-id="c966d-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="c966d-416">ASP.NET Çekirdek Modülü nasıl yüklenir yönergeleri [için](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)bkz.</span><span class="sxs-lookup"><span data-stu-id="c966d-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="c966d-417">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c966d-417">Configuration with web.config</span></span>

<span data-ttu-id="c966d-418">ASP.NET Çekirdek Modülü, sitenin `aspNetCore` `system.webServer` *web.config* dosyasındaki düğüm bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="c966d-419">Aşağıdaki *web.config* [dosyası, çerçeveye bağımlı](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) bir dağıtım için yayımlanır ve site isteklerini işlemek için ASP.NET Çekirdek Modül'u yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="c966d-420">Aşağıdaki *web.config* [kendi kendine yeten bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayınlanır:</span><span class="sxs-lookup"><span data-stu-id="c966d-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="c966d-421">Özellik, <xref:System.Configuration.SectionInformation.InheritInChildApplications*> konumu `false` [ \<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınılolmadığını belirtmek üzere ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c966d-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="c966d-422">Bir uygulama [Azure Uygulama Hizmeti'ne](https://azure.microsoft.com/services/app-service/) `stdoutLogFile` dağıtıldığında, `\\?\%home%\LogFiles\stdout`yol .</span><span class="sxs-lookup"><span data-stu-id="c966d-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c966d-423">Yol, hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne stdout günlüklerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c966d-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="c966d-424">IIS alt uygulama yapılandırması hakkında <xref:host-and-deploy/iis/index#sub-applications>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c966d-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="c966d-425">aspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="c966d-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="c966d-426">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="c966d-426">Attribute</span></span> | <span data-ttu-id="c966d-427">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c966d-427">Description</span></span> | <span data-ttu-id="c966d-428">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="c966d-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="c966d-429">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-429">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-430">**ProcessPath'de**belirtilen yürütülebilir bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="c966d-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="c966d-431">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-432">Doğruysa, **502.5 - İşlem Hatası** sayfası bastırılır ve *web.config'de* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="c966d-433">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-434">Doğruysa, belirteç istek başına 'MS-ASPNETCORE-WINAUTHTOKEN' üstbilgi olarak %ASPNETCORE_PORT'da dinleyerek alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="c966d-435">İstek başına bu belirteç için CloseHandle'ı aramak bu işlemin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="c966d-436">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-436">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-437">Barındırma modelini süreç içi (`InProcess`/`inprocess`) veya süreç dışı`OutOfProcess`/`outofprocess`olarak belirtir ( ).</span><span class="sxs-lookup"><span data-stu-id="c966d-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="c966d-438">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-439">Uygulama başına döndürülebilen **processPath** ayarında belirtilen işlemin örnek sayısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="c966d-440">&dagger;Süreç içi barındırma için, değer `1`.</span><span class="sxs-lookup"><span data-stu-id="c966d-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="c966d-441">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="c966d-442">Bu öznitelik gelecekteki bir sürümde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="c966d-443">Varsayılan:`1`</span><span class="sxs-lookup"><span data-stu-id="c966d-443">Default: `1`</span></span><br><span data-ttu-id="c966d-444">Dk:`1`</span><span class="sxs-lookup"><span data-stu-id="c966d-444">Min: `1`</span></span><br><span data-ttu-id="c966d-445">Max:`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="c966d-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="c966d-446">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-446">Required string attribute.</span></span></p><p><span data-ttu-id="c966d-447">HTTP isteklerini dinleyerek bir işlem başlatan yürütülebilir yol.</span><span class="sxs-lookup"><span data-stu-id="c966d-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="c966d-448">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-448">Relative paths are supported.</span></span> <span data-ttu-id="c966d-449">Yol ile `.`başlarsa, yol site köküne göre olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="c966d-450">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-451">**ProcessPath'de** belirtilen işlemin dakikada çökmesine izin verilme sayısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="c966d-452">Bu sınır aşılırsa, modül dakikanın geri kalanı için işlemi başlatmayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="c966d-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="c966d-453">Süreç içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="c966d-454">Varsayılan:`10`</span><span class="sxs-lookup"><span data-stu-id="c966d-454">Default: `10`</span></span><br><span data-ttu-id="c966d-455">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-455">Min: `0`</span></span><br><span data-ttu-id="c966d-456">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="c966d-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="c966d-457">İsteğe bağlı zaman alanı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="c966d-458">ASP.NET Çekirdek Modülü'nün %ASPNETCORE_PORT'de dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="c966d-459">ASP.NET Core 2.1 veya daha sonra sürümü ile gönderilen ASP.NET `requestTimeout` Çekirdek Modülü sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="c966d-460">Süreç içi barındırma için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="c966d-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="c966d-461">Süreç içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="c966d-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="c966d-462">Dize dakika ve saniye segmentleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="c966d-463">60 **60** değerinin dakika veya saniye olarak kullanılması *500 ile*sonuçlanır - Internal Server Hatası.</span><span class="sxs-lookup"><span data-stu-id="c966d-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="c966d-464">Varsayılan:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-464">Default: `00:02:00`</span></span><br><span data-ttu-id="c966d-465">Dk:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-465">Min: `00:00:00`</span></span><br><span data-ttu-id="c966d-466">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="c966d-467">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-468">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir inin düzgün bir şekilde kapanmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="c966d-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="c966d-469">Varsayılan:`10`</span><span class="sxs-lookup"><span data-stu-id="c966d-469">Default: `10`</span></span><br><span data-ttu-id="c966d-470">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-470">Min: `0`</span></span><br><span data-ttu-id="c966d-471">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="c966d-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="c966d-472">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-473">Modülün çalıştırılabilenin bağlantı noktasını dinleyerek bir işlem başlatmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="c966d-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="c966d-474">Bu zaman sınırı aşılırsa, modül işlemi öldürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="c966d-475">Modül, yeni bir istek aldığında işlemi yeniden başlatmaya çalışır ve uygulama son dakika içinde hızlı bir **şekilde FailsPerMinute** sayısını başlatamazsa sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="c966d-476">0 (sıfır) değeri sonsuz zaman kaybı olarak kabul **edilmez.**</span><span class="sxs-lookup"><span data-stu-id="c966d-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="c966d-477">Varsayılan:`120`</span><span class="sxs-lookup"><span data-stu-id="c966d-477">Default: `120`</span></span><br><span data-ttu-id="c966d-478">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-478">Min: `0`</span></span><br><span data-ttu-id="c966d-479">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="c966d-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="c966d-480">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-481">Doğruysa, **processPath** belirtilen işlem için **stdout** ve **stderr** **stdoutLogFile**belirtilen dosyaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="c966d-482">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-482">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-483">**ProcessPath'de** belirtilen işlemden **stdout** ve **stderr** günlüğe kaydedilmiş olan göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="c966d-484">Göreli yollar sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="c966d-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="c966d-485">Herhangi bir `.` yol ile başlayan site köküne göre ve diğer tüm yollar mutlak yollar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="c966d-486">Yol dosyasında sağlanan tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="c966d-487">Alt çizgi delimiters kullanarak, bir zaman damgası, işlem kimliği ve dosya uzantısı (*.log*) **stdoutLogFile** yolunun son bölümüne eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="c966d-488">Değer `.\logs\stdout` olarak veseği, 2/5/2018 tarihinde saat 19:41:32'de 1934 işlem kimliğiyle kaydedilirken, örneğin stdout günlüğü *stdout_20180205194132_1934.log* olarak kaydedilir. *logs*</span><span class="sxs-lookup"><span data-stu-id="c966d-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="c966d-489">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="c966d-489">Setting environment variables</span></span>

<span data-ttu-id="c966d-490">Öznitelikteki `processPath` işlem için ortam değişkenleri belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="c966d-491">Bir koleksiyon öğesinin `<environmentVariable>` alt öğesi ile bir ortam değişkeni belirtin. `<environmentVariables>`</span><span class="sxs-lookup"><span data-stu-id="c966d-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="c966d-492">Bu bölümde ayarlanan ortam değişkenleri sistem ortamı değişkenlerinden önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="c966d-493">Aşağıdaki örnekte iki ortam değişkeni kümesi yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="c966d-493">The following example sets two environment variables.</span></span> <span data-ttu-id="c966d-494">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını `Development`.</span><span class="sxs-lookup"><span data-stu-id="c966d-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="c966d-495">Geliştirici, geliştirici [özel durum sayfasını](xref:fundamentals/error-handling) hata ayıklarken Geliştirici Özel Durum Sayfasını yüklemeye zorlamak için bu değeri *web.config* dosyasında geçici olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="c966d-496">`CONFIG_DIR`geliştirici, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak için başlangıç değerini okuyan bir kod yazdığı kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="c966d-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="c966d-497">Ortamı doğrudan *web.config'de* ayarlamanın alternatifi, `<EnvironmentName>` özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına eklemektir.</span><span class="sxs-lookup"><span data-stu-id="c966d-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="c966d-498">Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="c966d-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="c966d-499">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` `Development` yalnızca Internet gibi güvenilmeyen ağlar tarafından erişilemeyen hazırlama ve sınama sunucularına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="c966d-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="c966d-500">app_offline.htm</span></span>

<span data-ttu-id="c966d-501">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="c966d-502">Uygulama tanımlanan saniye sayısından sonra çalışmaya `shutdownTimeLimit`devam ediyorsa, ASP.NET Çekirdek Modülü çalışma işlemini öldürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="c966d-503">*app_offline.htm* dosyası mevcut ken, ASP.NET Çekirdek Modülü *app_offline.htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="c966d-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="c966d-504">*app_offline.htm* dosyası kaldırıldığında, bir sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="c966d-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="c966d-505">İşlem dışı barındırma modelini kullanırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="c966d-506">Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="c966d-507">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="c966d-507">Start-up error page</span></span>

<span data-ttu-id="c966d-508">Hem süreç içi hem de süreç dışı barındırma, uygulamayı başlatamadıkları zaman özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="c966d-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="c966d-509">ASP.NET Çekirdek Modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500.0 - İşlem İçi/İşlem Dışı İşleyici Yük Hatası* kod sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="c966d-510">ASP.NET Çekirdek Modülü uygulamayı başlatamazsa, işlem içi barındırma için *500.30 - Start Failure* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="c966d-511">ASP.NET Çekirdek Modülü arka uç işlemini başlatamazsa veya arka uç işlemi başlarsa ancak yapılandırılan bağlantı noktasını dinlemezse, *502.5 - İşlem Hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="c966d-512">Bu sayfayı bastırmak ve varsayılan IIS 5xx durum kodu `disableStartUpErrorPage` sayfasına geri dönmek için özniteliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="c966d-513">Özel hata iletilerinin yapılandırılması hakkında daha fazla bilgi için [HTTP Hataları \<httpErrors>. ](/iis/configuration/system.webServer/httpErrors/)</span><span class="sxs-lookup"><span data-stu-id="c966d-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="c966d-514">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="c966d-514">Log creation and redirection</span></span>

<span data-ttu-id="c966d-515">ASP.NET Çekirdek Modülü, `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` öğenin öznitelikleri ve öznitelikleri ayarlanmışsa, stdout ve stderr konsol çıktısını diske yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="c966d-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="c966d-516">`stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="c966d-517">Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="c966d-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="c966d-518">İşlem geri dönüşümü/yeniden başlatma gerçekleşmedikçe günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="c966d-519">Günlüklerin tükettiği disk alanını sınırlamak ana bilgisayarın sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="c966d-520">Stdout günlüğünün kullanılması yalnızca IIS'de barındırma yaparken veya [Visual Studio ile IIS için geliştirme zamanı desteğini](xref:host-and-deploy/iis/development-time-iis-support)kullanırken sorun giderme uygulaması başlatma sorunları için önerilir, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken değil.</span><span class="sxs-lookup"><span data-stu-id="c966d-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="c966d-521">Stdout günlüğünü genel uygulama günlüğü amacıyla kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="c966d-522">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c966d-523">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="c966d-524">Günlük dosyası oluşturulduğunda otomatik olarak bir zaman damgası ve dosya uzantısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="c966d-525">Günlük dosya adı, `stdoutLogFile` zaman damgası, işlem kimliği ve dosya uzantısı *(.log*) yolun son kesimine (genellikle *stdout)* alt çizgiyle sınırlandırılmış olarak eklenerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="c966d-526">`stdoutLogFile` Yol *stdout*ile biterse , 2/5/2018 tarihinde 19:42:32 tarihinde oluşturulan 1934 PID ile bir uygulama için bir günlük *stdout_20180205194132_1934.log*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="c966d-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="c966d-527">Yanlışsa, `stdoutLogEnabled` uygulama başlatmada oluşan hatalar yakalanır ve 30 KB'a kadar olay günlüğüne yayılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="c966d-528">Başlatmadan sonra, tüm ek günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="c966d-529">Aşağıdaki örnek `aspNetCore` öğe, nispi yolda `.\log\`stdout günlüğe yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c966d-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="c966d-530">AppPool kullanıcı kimliğinin sağlanan yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="c966d-531">Azure Uygulama Hizmeti dağıtımı için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değerini `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="c966d-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c966d-532">Ortam `%home` değişkeni, Azure Uygulama Hizmeti tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c966d-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="c966d-533">Yol biçimleri hakkında daha fazla bilgi için [Windows sistemlerinde Dosya yolu biçimlerine](/dotnet/standard/io/file-path-formats)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="c966d-534">Geliştirilmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="c966d-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="c966d-535">ASP.NET Çekirdek Modülü gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="c966d-536">Öğeyi `<handlerSettings>` `<aspNetCore>` *web.config'deki*öğeye ekleyin. Tanılama `debugLevel` `TRACE` bilgilerinin daha yüksek bir sadakatini ortaya çıkaracak şekilde ayarlanın:</span><span class="sxs-lookup"><span data-stu-id="c966d-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="c966d-537">`<handlerSetting>` Değere sağlanan yoldaki klasörler (önceki örnekte*günlükler)* modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c966d-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="c966d-538">Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="c966d-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="c966d-539">Hata ayıklama`debugLevel`düzeyi ( ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="c966d-540">Düzeyler (en azdan en ayrıntılısıraya kadar):</span><span class="sxs-lookup"><span data-stu-id="c966d-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="c966d-541">HATA</span><span class="sxs-lookup"><span data-stu-id="c966d-541">ERROR</span></span>
* <span data-ttu-id="c966d-542">UYARI</span><span class="sxs-lookup"><span data-stu-id="c966d-542">WARNING</span></span>
* <span data-ttu-id="c966d-543">Bilgi</span><span class="sxs-lookup"><span data-stu-id="c966d-543">INFO</span></span>
* <span data-ttu-id="c966d-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="c966d-544">TRACE</span></span>

<span data-ttu-id="c966d-545">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="c966d-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="c966d-546">Konsol</span><span class="sxs-lookup"><span data-stu-id="c966d-546">CONSOLE</span></span>
* <span data-ttu-id="c966d-547">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c966d-547">EVENTLOG</span></span>
* <span data-ttu-id="c966d-548">DOSYA</span><span class="sxs-lookup"><span data-stu-id="c966d-548">FILE</span></span>

<span data-ttu-id="c966d-549">İşleyici ayarları ortam değişkenleri aracılığıyla da sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="c966d-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="c966d-550">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Hata ayıklama günlüğü dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="c966d-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="c966d-551">(Varsayılan: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="c966d-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="c966d-552">`ASPNETCORE_MODULE_DEBUG`&ndash; Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="c966d-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="c966d-553">Hata **not** ayıklama günlüğekaydetmeyi dağıtımda bir sorunu gidermek için gerekenden daha uzun süre etkin bırakmayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="c966d-554">Günlüğün boyutu sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="c966d-554">The size of the log isn't limited.</span></span> <span data-ttu-id="c966d-555">Hata ayıklama günlüğünü etkin bırakmak, kullanılabilir disk alanını tüketebilir ve sunucuyu veya uygulama hizmetini kilitleyebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="c966d-556">*Web.config* dosyasındaki `aspNetCore` öğenin bir örneği için [web.config ile Yapılandırma'ya](#configuration-with-webconfig) bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="c966d-557">Proxy yapılandırması HTTP protokolü ve eşleştirme belirteci kullanır</span><span class="sxs-lookup"><span data-stu-id="c966d-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="c966d-558">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c966d-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="c966d-559">ASP.NET Çekirdek Modülü ile Kestrel arasında oluşturulan proxy HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="c966d-560">Modül ve Kestrel arasındaki trafiği sunucunun dışında bir konumdan gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="c966d-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="c966d-561">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından yerine getirilmiş olduğunu ve başka bir kaynaktan gelmediğini garanti etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="c966d-562">Eşleştirme belirteci modül tarafından oluşturulur ve`ASPNETCORE_TOKEN`bir ortam değişkeni () olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="c966d-563">Eşleştirme belirteci, her yakın istekte`MS-ASPNETCORE-TOKEN`bir üstbilgi () olarak da ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="c966d-564">IIS Middleware, eşleştirme belirteç üstbilgi değerinin ortam değişken değeriyle eşleştiğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="c966d-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="c966d-565">Belirteç değerleri eşleşmezse, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="c966d-566">Eşleştirme belirteç ortamı değişkeni ve modül ile Kestrel arasındaki trafiğe sunucunun dışında bir konumdan erişilemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="c966d-567">Eşleştirme belirteç değerini bilmeden, saldırgan IIS Middleware'de denetimi atlayan istekler gönderemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="c966d-568">IIS Paylaşılan Yapılandırmalı ASP.NET Çekirdek Modülü</span><span class="sxs-lookup"><span data-stu-id="c966d-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="c966d-569">ASP.NET Çekirdek Modülü yükleyici, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="c966d-570">Yerel sistem hesabı, IIS Paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değişiklik izni olmadığından, yükleci, paylaşımdaki *applicationHost.config* dosyasındaki modül ayarlarını yapılandırmaya çalışırken erişim reddedilen bir hata atar.</span><span class="sxs-lookup"><span data-stu-id="c966d-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="c966d-571">IIS yüklemesi ile aynı makinede bir IIS Paylaşılan Yapılandırma kullanırken, `OPT_NO_SHARED_CONFIG_CHECK` parametre ayarlanmış `1`ASP.NET Core Hosting Bundle yükleyicisini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c966d-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="c966d-572">Paylaşılan yapılandırmaya giden yol IIS yüklemeyle aynı makinede değilse aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="c966d-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="c966d-573">IIS Paylaşılan Yapılandırmasını devre dışı kınla.</span><span class="sxs-lookup"><span data-stu-id="c966d-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="c966d-574">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-574">Run the installer.</span></span>
1. <span data-ttu-id="c966d-575">Paylaşıma güncelleştirilmiş *applicationHost.config* dosyasını dışa aktarın.</span><span class="sxs-lookup"><span data-stu-id="c966d-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="c966d-576">IIS Paylaşılan Yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c966d-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="c966d-577">Modül sürümü ve Hosting Paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="c966d-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="c966d-578">Yüklü ASP.NET Çekirdek Modülü sürümünü belirlemek için:</span><span class="sxs-lookup"><span data-stu-id="c966d-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="c966d-579">Barındırma sisteminde *% windir%\System32\inetsrv'e*gidin.</span><span class="sxs-lookup"><span data-stu-id="c966d-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="c966d-580">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="c966d-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="c966d-581">Dosyaya sağ tıklayın ve bağlamsal menüden **Özellikler'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="c966d-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="c966d-582">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve Ürün **sürümü** modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="c966d-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="c966d-583">Modül için Hosting Paketi yükleyici günlükleri *C\\\\bulunur: Kullanıcılar\\%UserName%\\AppData Local\\Temp*. Dosya zaman *damgası>_000_AspNetCoreModule_x64.log dd_DotNetCoreWinSvrHosting__\<* adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="c966d-584">Modül, şema ve yapılandırma dosya konumları</span><span class="sxs-lookup"><span data-stu-id="c966d-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="c966d-585">Modül</span><span class="sxs-lookup"><span data-stu-id="c966d-585">Module</span></span>

<span data-ttu-id="c966d-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c966d-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="c966d-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-589">%ProgramFiles%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="c966d-590">%ProgramFiles(x86)%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="c966d-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c966d-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="c966d-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-594">%ProgramFiles%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="c966d-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="c966d-596">Şema</span><span class="sxs-lookup"><span data-stu-id="c966d-596">Schema</span></span>

<span data-ttu-id="c966d-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c966d-597">**IIS**</span></span>

* <span data-ttu-id="c966d-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="c966d-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="c966d-600">**IIS Ekspresi**</span><span class="sxs-lookup"><span data-stu-id="c966d-600">**IIS Express**</span></span>

* <span data-ttu-id="c966d-601">%ProgramFiles%\IIS Express\config\şema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="c966d-602">%ProgramFiles%\IIS Express\config\şema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="c966d-603">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c966d-603">Configuration</span></span>

<span data-ttu-id="c966d-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c966d-604">**IIS**</span></span>

* <span data-ttu-id="c966d-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="c966d-606">**IIS Ekspresi**</span><span class="sxs-lookup"><span data-stu-id="c966d-606">**IIS Express**</span></span>

* <span data-ttu-id="c966d-607">Görsel Stüdyo: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="c966d-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="c966d-609">Dosyalar *applicationHost.config* dosyasında *aspnetcore* arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c966d-610">ASP.NET Çekirdek Modülü, Web isteklerini Core ASP.NET uygulamalarını geri almak için iletmek için IIS boru hattına takılan yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="c966d-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="c966d-611">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="c966d-611">Supported Windows versions:</span></span>

* <span data-ttu-id="c966d-612">Windows 7 veya sonraki</span><span class="sxs-lookup"><span data-stu-id="c966d-612">Windows 7 or later</span></span>
* <span data-ttu-id="c966d-613">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="c966d-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c966d-614">Modül sadece Kerkenez ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-614">The module only works with Kestrel.</span></span> <span data-ttu-id="c966d-615">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="c966d-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="c966d-616">Core uygulamaları ASP.NET IIS alt işleminden ayrı bir işlemde çalıştığı için, modül aynı zamanda işlem yönetimini de işler.</span><span class="sxs-lookup"><span data-stu-id="c966d-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="c966d-617">Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve çökmediğinde uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="c966d-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="c966d-618">Bu aslında [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS'de işlem gören ASP.NET 4.x uygulamasında görülen davranışla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="c966d-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c966d-619">Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve bir uygulama arasındaki ilişkiyi göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c966d-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="c966d-621">İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir.</span><span class="sxs-lookup"><span data-stu-id="c966d-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c966d-622">Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="c966d-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c966d-623">Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.</span><span class="sxs-lookup"><span data-stu-id="c966d-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c966d-624">Modül başlangıçta bir ortam değişkeni üzerinden bağlantı noktasını belirtir ve [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde `http://localhost:{port}`yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c966d-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="c966d-625">Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c966d-626">Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c966d-627">Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c966d-628">Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın.</span><span class="sxs-lookup"><span data-stu-id="c966d-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c966d-629">IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="c966d-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c966d-630">Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.</span><span class="sxs-lookup"><span data-stu-id="c966d-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c966d-631">Windows Kimlik Doğrulama gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="c966d-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="c966d-632">ASP.NET Çekirdek Modülü ile etkin olan IIS modülleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/iis/modules></span><span class="sxs-lookup"><span data-stu-id="c966d-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="c966d-633">ASP.NET Çekirdek Modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="c966d-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="c966d-634">Alt işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="c966d-635">Sorun giderme başlangıç sorunları için dosya depolamasına stdout çıktısını günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c966d-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="c966d-636">İleri Windows kimlik doğrulama belirteçleri.</span><span class="sxs-lookup"><span data-stu-id="c966d-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="c966d-637">ASP.NET Çekirdek Modülü nasıl yüklenir ve kullanılır?</span><span class="sxs-lookup"><span data-stu-id="c966d-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="c966d-638">ASP.NET Çekirdek Modülü nasıl yüklenir yönergeleri [için](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)bkz.</span><span class="sxs-lookup"><span data-stu-id="c966d-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="c966d-639">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c966d-639">Configuration with web.config</span></span>

<span data-ttu-id="c966d-640">ASP.NET Çekirdek Modülü, sitenin `aspNetCore` `system.webServer` *web.config* dosyasındaki düğüm bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="c966d-641">Aşağıdaki *web.config* [dosyası, çerçeveye bağımlı](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) bir dağıtım için yayımlanır ve site isteklerini işlemek için ASP.NET Çekirdek Modül'u yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="c966d-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="c966d-642">Aşağıdaki *web.config* [kendi kendine yeten bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayınlanır:</span><span class="sxs-lookup"><span data-stu-id="c966d-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="c966d-643">Bir uygulama [Azure Uygulama Hizmeti'ne](https://azure.microsoft.com/services/app-service/) `stdoutLogFile` dağıtıldığında, `\\?\%home%\LogFiles\stdout`yol .</span><span class="sxs-lookup"><span data-stu-id="c966d-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c966d-644">Yol, hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne stdout günlüklerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c966d-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="c966d-645">IIS alt uygulama yapılandırması hakkında <xref:host-and-deploy/iis/index#sub-applications>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c966d-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="c966d-646">aspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="c966d-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="c966d-647">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="c966d-647">Attribute</span></span> | <span data-ttu-id="c966d-648">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c966d-648">Description</span></span> | <span data-ttu-id="c966d-649">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="c966d-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="c966d-650">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-650">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-651">**ProcessPath'de**belirtilen yürütülebilir bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="c966d-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="c966d-652">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-653">Doğruysa, **502.5 - İşlem Hatası** sayfası bastırılır ve *web.config'de* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="c966d-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="c966d-654">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-655">Doğruysa, belirteç istek başına 'MS-ASPNETCORE-WINAUTHTOKEN' üstbilgi olarak %ASPNETCORE_PORT'da dinleyerek alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="c966d-656">İstek başına bu belirteç için CloseHandle'ı aramak bu işlemin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="c966d-657">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-658">Uygulama başına döndürülebilen **processPath** ayarında belirtilen işlemin örnek sayısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="c966d-659">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="c966d-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="c966d-660">Bu öznitelik gelecekteki bir sürümde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="c966d-661">Varsayılan:`1`</span><span class="sxs-lookup"><span data-stu-id="c966d-661">Default: `1`</span></span><br><span data-ttu-id="c966d-662">Dk:`1`</span><span class="sxs-lookup"><span data-stu-id="c966d-662">Min: `1`</span></span><br><span data-ttu-id="c966d-663">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="c966d-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="c966d-664">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-664">Required string attribute.</span></span></p><p><span data-ttu-id="c966d-665">HTTP isteklerini dinleyerek bir işlem başlatan yürütülebilir yol.</span><span class="sxs-lookup"><span data-stu-id="c966d-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="c966d-666">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-666">Relative paths are supported.</span></span> <span data-ttu-id="c966d-667">Yol ile `.`başlarsa, yol site köküne göre olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="c966d-668">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-669">**ProcessPath'de** belirtilen işlemin dakikada çökmesine izin verilme sayısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="c966d-670">Bu sınır aşılırsa, modül dakikanın geri kalanı için işlemi başlatmayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="c966d-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="c966d-671">Varsayılan:`10`</span><span class="sxs-lookup"><span data-stu-id="c966d-671">Default: `10`</span></span><br><span data-ttu-id="c966d-672">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-672">Min: `0`</span></span><br><span data-ttu-id="c966d-673">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="c966d-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="c966d-674">İsteğe bağlı zaman alanı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="c966d-675">ASP.NET Çekirdek Modülü'nün %ASPNETCORE_PORT'de dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="c966d-676">ASP.NET Core 2.1 veya daha sonra sürümü ile gönderilen ASP.NET `requestTimeout` Çekirdek Modülü sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="c966d-677">Varsayılan:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-677">Default: `00:02:00`</span></span><br><span data-ttu-id="c966d-678">Dk:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-678">Min: `00:00:00`</span></span><br><span data-ttu-id="c966d-679">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="c966d-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="c966d-680">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-681">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir inin düzgün bir şekilde kapanmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="c966d-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="c966d-682">Varsayılan:`10`</span><span class="sxs-lookup"><span data-stu-id="c966d-682">Default: `10`</span></span><br><span data-ttu-id="c966d-683">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-683">Min: `0`</span></span><br><span data-ttu-id="c966d-684">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="c966d-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="c966d-685">İsteğe bağlı birer sayışma özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="c966d-686">Modülün çalıştırılabilenin bağlantı noktasını dinleyerek bir işlem başlatmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="c966d-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="c966d-687">Bu zaman sınırı aşılırsa, modül işlemi öldürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="c966d-688">Modül, yeni bir istek aldığında işlemi yeniden başlatmaya çalışır ve uygulama son dakika içinde hızlı bir **şekilde FailsPerMinute** sayısını başlatamazsa sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="c966d-689">0 (sıfır) değeri sonsuz zaman kaybı olarak kabul **edilmez.**</span><span class="sxs-lookup"><span data-stu-id="c966d-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="c966d-690">Varsayılan:`120`</span><span class="sxs-lookup"><span data-stu-id="c966d-690">Default: `120`</span></span><br><span data-ttu-id="c966d-691">Dk:`0`</span><span class="sxs-lookup"><span data-stu-id="c966d-691">Min: `0`</span></span><br><span data-ttu-id="c966d-692">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="c966d-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="c966d-693">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c966d-694">Doğruysa, **processPath** belirtilen işlem için **stdout** ve **stderr** **stdoutLogFile**belirtilen dosyaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="c966d-695">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="c966d-695">Optional string attribute.</span></span></p><p><span data-ttu-id="c966d-696">**ProcessPath'de** belirtilen işlemden **stdout** ve **stderr** günlüğe kaydedilmiş olan göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="c966d-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="c966d-697">Göreli yollar sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="c966d-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="c966d-698">Herhangi bir `.` yol ile başlayan site köküne göre ve diğer tüm yollar mutlak yollar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="c966d-699">Modülün günlük dosyasını oluşturabilmesi için yolda sağlanan klasörlerin bulunması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c966d-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="c966d-700">Alt çizgi delimiters kullanarak, bir zaman damgası, işlem kimliği ve dosya uzantısı (*.log*) **stdoutLogFile** yolunun son bölümüne eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="c966d-701">Değer `.\logs\stdout` olarak veseği, 2/5/2018 tarihinde saat 19:41:32'de 1934 işlem kimliğiyle kaydedilirken, örneğin stdout günlüğü *stdout_20180205194132_1934.log* olarak kaydedilir. *logs*</span><span class="sxs-lookup"><span data-stu-id="c966d-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="c966d-702">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="c966d-702">Setting environment variables</span></span>

<span data-ttu-id="c966d-703">Öznitelikteki `processPath` işlem için ortam değişkenleri belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="c966d-704">Bir koleksiyon öğesinin `<environmentVariable>` alt öğesi ile bir ortam değişkeni belirtin. `<environmentVariables>`</span><span class="sxs-lookup"><span data-stu-id="c966d-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="c966d-705">Bu bölümde ayarlanan ortam değişkenleri, aynı ada göre ayarlanmış sistem ortamı değişkenleriyle çakışMaktadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="c966d-706">Hem *web.config* dosyasında hem de Windows'daki sistem düzeyinde bir ortam değişkeni ayarlanırsa, *web.config* dosyasındaki değer, `ASPNETCORE_ENVIRONMENT: Development;Development`uygulamanın başlatılmasını engelleyen sistem ortamı değişken değerine (örneğin) eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="c966d-707">Aşağıdaki örnekte iki ortam değişkeni kümesi yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="c966d-707">The following example sets two environment variables.</span></span> <span data-ttu-id="c966d-708">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını `Development`.</span><span class="sxs-lookup"><span data-stu-id="c966d-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="c966d-709">Geliştirici, geliştirici [özel durum sayfasını](xref:fundamentals/error-handling) hata ayıklarken Geliştirici Özel Durum Sayfasını yüklemeye zorlamak için bu değeri *web.config* dosyasında geçici olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="c966d-710">`CONFIG_DIR`geliştirici, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak için başlangıç değerini okuyan bir kod yazdığı kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="c966d-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="c966d-711">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` `Development` yalnızca Internet gibi güvenilmeyen ağlar tarafından erişilemeyen hazırlama ve sınama sunucularına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="c966d-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="c966d-712">app_offline.htm</span></span>

<span data-ttu-id="c966d-713">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="c966d-714">Uygulama tanımlanan saniye sayısından sonra çalışmaya `shutdownTimeLimit`devam ediyorsa, ASP.NET Çekirdek Modülü çalışma işlemini öldürür.</span><span class="sxs-lookup"><span data-stu-id="c966d-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="c966d-715">*app_offline.htm* dosyası mevcut ken, ASP.NET Çekirdek Modülü *app_offline.htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="c966d-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="c966d-716">*app_offline.htm* dosyası kaldırıldığında, bir sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="c966d-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="c966d-717">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="c966d-717">Start-up error page</span></span>

<span data-ttu-id="c966d-718">ASP.NET Çekirdek Modülü arka uç işlemini başlatamazsa veya arka uç işlemi başlarsa ancak yapılandırılan bağlantı noktasını dinlemezse, *502.5 - İşlem Hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="c966d-719">Bu sayfayı bastırmak ve varsayılan IIS 502 durum kodu `disableStartUpErrorPage` sayfasına geri dönmek için özniteliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="c966d-720">Özel hata iletilerinin yapılandırılması hakkında daha fazla bilgi için [HTTP Hataları \<httpErrors>. ](/iis/configuration/system.webServer/httpErrors/)</span><span class="sxs-lookup"><span data-stu-id="c966d-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 İşlem Hatası Durum Kodu Sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="c966d-722">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="c966d-722">Log creation and redirection</span></span>

<span data-ttu-id="c966d-723">ASP.NET Çekirdek Modülü, `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` öğenin öznitelikleri ve öznitelikleri ayarlanmışsa, stdout ve stderr konsol çıktısını diske yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="c966d-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="c966d-724">`stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="c966d-725">Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="c966d-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="c966d-726">İşlem geri dönüşümü/yeniden başlatma gerçekleşmedikçe günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="c966d-727">Günlüklerin tükettiği disk alanını sınırlamak ana bilgisayarın sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="c966d-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="c966d-728">Stdout günlüğünün kullanılması yalnızca IIS'de barındırma yaparken veya [Visual Studio ile IIS için geliştirme zamanı desteğini](xref:host-and-deploy/iis/development-time-iis-support)kullanırken sorun giderme uygulaması başlatma sorunları için önerilir, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken değil.</span><span class="sxs-lookup"><span data-stu-id="c966d-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="c966d-729">Stdout günlüğünü genel uygulama günlüğü amacıyla kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="c966d-730">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="c966d-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c966d-731">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="c966d-732">Günlük dosyası oluşturulduğunda otomatik olarak bir zaman damgası ve dosya uzantısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="c966d-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="c966d-733">Günlük dosya adı, `stdoutLogFile` zaman damgası, işlem kimliği ve dosya uzantısı *(.log*) yolun son kesimine (genellikle *stdout)* alt çizgiyle sınırlandırılmış olarak eklenerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c966d-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="c966d-734">`stdoutLogFile` Yol *stdout*ile biterse , 2/5/2018 tarihinde 19:42:32 tarihinde oluşturulan 1934 PID ile bir uygulama için bir günlük *stdout_20180205194132_1934.log*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="c966d-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="c966d-735">Aşağıdaki örnek `aspNetCore` öğe, nispi yolda `.\log\`stdout günlüğe yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c966d-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="c966d-736">AppPool kullanıcı kimliğinin sağlanan yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c966d-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="c966d-737">Azure Uygulama Hizmeti dağıtımı için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değerini `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="c966d-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c966d-738">Ortam `%home` değişkeni, Azure Uygulama Hizmeti tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c966d-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="c966d-739">Günlük filtresi kuralları oluşturmak için, ASP.NET Core günlük belgelerinin [Yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [Günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="c966d-740">Yol biçimleri hakkında daha fazla bilgi için [Windows sistemlerinde Dosya yolu biçimlerine](/dotnet/standard/io/file-path-formats)bakın.</span><span class="sxs-lookup"><span data-stu-id="c966d-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="c966d-741">Proxy yapılandırması HTTP protokolü ve eşleştirme belirteci kullanır</span><span class="sxs-lookup"><span data-stu-id="c966d-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="c966d-742">ASP.NET Çekirdek Modülü ile Kestrel arasında oluşturulan proxy HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="c966d-743">Modül ve Kestrel arasındaki trafiği sunucunun dışında bir konumdan gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="c966d-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="c966d-744">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından yerine getirilmiş olduğunu ve başka bir kaynaktan gelmediğini garanti etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="c966d-745">Eşleştirme belirteci modül tarafından oluşturulur ve`ASPNETCORE_TOKEN`bir ortam değişkeni () olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="c966d-746">Eşleştirme belirteci, her yakın istekte`MS-ASPNETCORE-TOKEN`bir üstbilgi () olarak da ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c966d-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="c966d-747">IIS Middleware, eşleştirme belirteç üstbilgi değerinin ortam değişken değeriyle eşleştiğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="c966d-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="c966d-748">Belirteç değerleri eşleşmezse, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="c966d-749">Eşleştirme belirteç ortamı değişkeni ve modül ile Kestrel arasındaki trafiğe sunucunun dışında bir konumdan erişilemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="c966d-750">Eşleştirme belirteç değerini bilmeden, saldırgan IIS Middleware'de denetimi atlayan istekler gönderemez.</span><span class="sxs-lookup"><span data-stu-id="c966d-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="c966d-751">IIS Paylaşılan Yapılandırmalı ASP.NET Çekirdek Modülü</span><span class="sxs-lookup"><span data-stu-id="c966d-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="c966d-752">ASP.NET Çekirdek Modülü yükleyici, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="c966d-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="c966d-753">Yerel sistem hesabı, IIS Paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değişiklik izni olmadığından, yükleci, paylaşımdaki *applicationHost.config* dosyasındaki modül ayarlarını yapılandırmaya çalışırken erişim reddedilen bir hata atar.</span><span class="sxs-lookup"><span data-stu-id="c966d-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="c966d-754">IIS Paylaşılan Yapılandırması kullanırken aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="c966d-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="c966d-755">IIS Paylaşılan Yapılandırmasını devre dışı kınla.</span><span class="sxs-lookup"><span data-stu-id="c966d-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="c966d-756">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c966d-756">Run the installer.</span></span>
1. <span data-ttu-id="c966d-757">Paylaşıma güncelleştirilmiş *applicationHost.config* dosyasını dışa aktarın.</span><span class="sxs-lookup"><span data-stu-id="c966d-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="c966d-758">IIS Paylaşılan Yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c966d-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="c966d-759">Modül sürümü ve Hosting Paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="c966d-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="c966d-760">Yüklü ASP.NET Çekirdek Modülü sürümünü belirlemek için:</span><span class="sxs-lookup"><span data-stu-id="c966d-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="c966d-761">Barındırma sisteminde *% windir%\System32\inetsrv'e*gidin.</span><span class="sxs-lookup"><span data-stu-id="c966d-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="c966d-762">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="c966d-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="c966d-763">Dosyaya sağ tıklayın ve bağlamsal menüden **Özellikler'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="c966d-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="c966d-764">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve Ürün **sürümü** modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="c966d-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="c966d-765">Modül için Hosting Paketi yükleyici günlükleri *C\\\\bulunur: Kullanıcılar\\%UserName%\\AppData Local\\Temp*. Dosya zaman *damgası>_000_AspNetCoreModule_x64.log dd_DotNetCoreWinSvrHosting__\<* adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c966d-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="c966d-766">Modül, şema ve yapılandırma dosya konumları</span><span class="sxs-lookup"><span data-stu-id="c966d-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="c966d-767">Modül</span><span class="sxs-lookup"><span data-stu-id="c966d-767">Module</span></span>

<span data-ttu-id="c966d-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c966d-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="c966d-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="c966d-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c966d-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="c966d-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="c966d-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c966d-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="c966d-774">Şema</span><span class="sxs-lookup"><span data-stu-id="c966d-774">Schema</span></span>

<span data-ttu-id="c966d-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c966d-775">**IIS**</span></span>

* <span data-ttu-id="c966d-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="c966d-777">**IIS Ekspresi**</span><span class="sxs-lookup"><span data-stu-id="c966d-777">**IIS Express**</span></span>

* <span data-ttu-id="c966d-778">%ProgramFiles%\IIS Express\config\şema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c966d-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="c966d-779">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c966d-779">Configuration</span></span>

<span data-ttu-id="c966d-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c966d-780">**IIS**</span></span>

* <span data-ttu-id="c966d-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="c966d-782">**IIS Ekspresi**</span><span class="sxs-lookup"><span data-stu-id="c966d-782">**IIS Express**</span></span>

* <span data-ttu-id="c966d-783">Görsel Stüdyo: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="c966d-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="c966d-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="c966d-785">Dosyalar *applicationHost.config* dosyasında *aspnetcore* arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="c966d-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c966d-786">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c966d-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="c966d-787">[ASP.NET Çekirdek Modül başvuru kaynağı (ana dal)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Belirli bir sürümü seçmek `release/3.1`için **Şube** açılır listesini kullanın (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="c966d-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
