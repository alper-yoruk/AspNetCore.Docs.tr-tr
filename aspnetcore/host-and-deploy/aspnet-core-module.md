---
title: ASP.NET Core Modülü
author: rick-anderson
description: ASP.NET Core uygulamalarını barındırmak için ASP.NET Core modülünü yapılandırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: e16735ec06e6d84118e5949fe56c514015105242
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758586"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="5bacc-103">ASP.NET Core Modülü</span><span class="sxs-lookup"><span data-stu-id="5bacc-103">ASP.NET Core Module</span></span>

<span data-ttu-id="5bacc-104">, [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris](https://github.com/Tratcher)No, [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)ve ka [kotalık](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="5bacc-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bacc-105">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="5bacc-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5bacc-106">`w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5bacc-107">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5bacc-108">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="5bacc-108">Supported Windows versions:</span></span>

* <span data-ttu-id="5bacc-109">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5bacc-109">Windows 7 or later</span></span>
* <span data-ttu-id="5bacc-110">Windows Server 2012 R2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5bacc-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="5bacc-111">İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5bacc-112">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5bacc-113">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="5bacc-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5bacc-114">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5bacc-115">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="5bacc-115">In-process hosting model</span></span>

<span data-ttu-id="5bacc-116">Uygulamalar, işlem içi barındırma modelinde varsayılan olarak ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5bacc-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="5bacc-117">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5bacc-118">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="5bacc-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5bacc-119">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="5bacc-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5bacc-120">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5bacc-121">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5bacc-122">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5bacc-123">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5bacc-124">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5bacc-125">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-125">Use one app pool per app.</span></span>

* <span data-ttu-id="5bacc-126">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtıma el ile bir [app_offline.htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5bacc-127">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5bacc-128">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5bacc-129">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="5bacc-129">Client disconnects are detected.</span></span> <span data-ttu-id="5bacc-130">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5bacc-131">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *w3wp.exe*için *c:\Windows\System32\inetsrv* ).</span><span class="sxs-lookup"><span data-stu-id="5bacc-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5bacc-132">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="5bacc-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5bacc-133">Yöntemini çağırın `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5bacc-134"><xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5bacc-135">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5bacc-136">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5bacc-137">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="5bacc-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="5bacc-138">[Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5bacc-139">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="5bacc-139">Out-of-process hosting model</span></span>

<span data-ttu-id="5bacc-140">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini `OutOfProcess` Proje dosyasında (*. csproj*) olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="5bacc-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5bacc-141">İşlem içi barındırma `InProcess` , varsayılan değer olan ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="5bacc-142">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5bacc-143">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5bacc-144">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5bacc-145">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5bacc-146">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5bacc-147">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-147">Hosting model changes</span></span>

<span data-ttu-id="5bacc-148">Ayar, `hostingModel` *web.config* dosyasında değiştirilirse ( [web.configile yapılandırma](#configuration-with-webconfig) bölümünde açıklanan), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="5bacc-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5bacc-149">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5bacc-150">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5bacc-151">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="5bacc-151">Process name</span></span>

<span data-ttu-id="5bacc-152">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="5bacc-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="5bacc-153">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5bacc-154">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="5bacc-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5bacc-155">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5bacc-156">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5bacc-157">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5bacc-158">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="5bacc-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5bacc-159">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="5bacc-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5bacc-160">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5bacc-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5bacc-161">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5bacc-161">Configuration with web.config</span></span>

<span data-ttu-id="5bacc-162">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5bacc-163">Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5bacc-164">Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5bacc-165"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="5bacc-166">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5bacc-167">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5bacc-168">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="5bacc-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5bacc-169">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5bacc-170">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="5bacc-170">Attribute</span></span> | <span data-ttu-id="5bacc-171">Açıklama</span><span class="sxs-lookup"><span data-stu-id="5bacc-171">Description</span></span> | <span data-ttu-id="5bacc-172">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="5bacc-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5bacc-173">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-173">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-174">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5bacc-175">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-176">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5bacc-177">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-178">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5bacc-179">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="5bacc-180">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-180">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-181">Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="5bacc-182">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-183">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5bacc-184">&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5bacc-185">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5bacc-186">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="5bacc-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5bacc-187">Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="5bacc-187">Default: `1`</span></span><br><span data-ttu-id="5bacc-188">Min`1`</span><span class="sxs-lookup"><span data-stu-id="5bacc-188">Min: `1`</span></span><br><span data-ttu-id="5bacc-189">Biçimlendir`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="5bacc-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="5bacc-190">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-190">Required string attribute.</span></span></p><p><span data-ttu-id="5bacc-191">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="5bacc-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5bacc-192">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-192">Relative paths are supported.</span></span> <span data-ttu-id="5bacc-193">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5bacc-194">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-195">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5bacc-196">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="5bacc-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5bacc-197">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="5bacc-198">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="5bacc-198">Default: `10`</span></span><br><span data-ttu-id="5bacc-199">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-199">Min: `0`</span></span><br><span data-ttu-id="5bacc-200">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="5bacc-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5bacc-201">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5bacc-202">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5bacc-203">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5bacc-204">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5bacc-205">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="5bacc-206">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="5bacc-207">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="5bacc-208">Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-208">Default: `00:02:00`</span></span><br><span data-ttu-id="5bacc-209">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-209">Min: `00:00:00`</span></span><br><span data-ttu-id="5bacc-210">Biçimlendir`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5bacc-211">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-212">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="5bacc-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5bacc-213">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="5bacc-213">Default: `10`</span></span><br><span data-ttu-id="5bacc-214">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-214">Min: `0`</span></span><br><span data-ttu-id="5bacc-215">Biçimlendir`600`</span><span class="sxs-lookup"><span data-stu-id="5bacc-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5bacc-216">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-217">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="5bacc-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5bacc-218">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="5bacc-218">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="5bacc-219">*İşlem içi*barındırma sırasında: **işlem yeniden başlatılmaz** ve **rapidFailsPerMinute** ayarını **kullanmaz.**</span><span class="sxs-lookup"><span data-stu-id="5bacc-219">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="5bacc-220">*İşlem dışı*barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** sayısını başlatamadıkça sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="5bacc-220">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5bacc-221">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="5bacc-221">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5bacc-222">Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="5bacc-222">Default: `120`</span></span><br><span data-ttu-id="5bacc-223">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-223">Min: `0`</span></span><br><span data-ttu-id="5bacc-224">Biçimlendir`3600`</span><span class="sxs-lookup"><span data-stu-id="5bacc-224">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5bacc-225">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-225">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-226">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-226">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5bacc-227">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-227">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-228">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-228">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5bacc-229">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-229">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5bacc-230">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-230">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5bacc-231">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-231">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5bacc-232">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-232">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5bacc-233">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-233">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="5bacc-234">Ortam değişkenlerini belirleme</span><span class="sxs-lookup"><span data-stu-id="5bacc-234">Set environment variables</span></span>

<span data-ttu-id="5bacc-235">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-235">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5bacc-236">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-236">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5bacc-237">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-237">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="5bacc-238">Aşağıdaki örnek *web.config*iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-238">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5bacc-239">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-239">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5bacc-240">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-240">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5bacc-241">Ortamı doğrudan *web.config* olarak ayarlamanın alternatifi, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-241">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="5bacc-242">Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5bacc-242">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="5bacc-243">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-243">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5bacc-244">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5bacc-244">app_offline.htm</span></span>

<span data-ttu-id="5bacc-245">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-245">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5bacc-246">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-246">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5bacc-247">*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-247">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5bacc-248">*app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-248">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="5bacc-249">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-249">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5bacc-250">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-250">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5bacc-251">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="5bacc-251">Start-up error page</span></span>

<span data-ttu-id="5bacc-252">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-252">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5bacc-253">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-253">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5bacc-254">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-254">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5bacc-255">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-255">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5bacc-256">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-256">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5bacc-257">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="5bacc-257">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5bacc-258">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="5bacc-258">Log creation and redirection</span></span>

<span data-ttu-id="5bacc-259">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-259">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5bacc-260">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-260">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5bacc-261">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="5bacc-261">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5bacc-262">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-262">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5bacc-263">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-263">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5bacc-264">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-264">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5bacc-265">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-265">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5bacc-266">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-266">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5bacc-267">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5bacc-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5bacc-268">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-268">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5bacc-269">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-269">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5bacc-270">`stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-270">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5bacc-271">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-271">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5bacc-272">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-272">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="5bacc-273">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-273">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5bacc-274">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-274">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="5bacc-275">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-275">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5bacc-276">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-276">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5bacc-277">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-277">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="5bacc-278">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="5bacc-278">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5bacc-279">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-279">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5bacc-280">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-280">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5bacc-281">`<handlerSettings>`Öğesini `<aspNetCore>` *web.config*öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="5bacc-281">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5bacc-282">Yoldaki tüm klasörler (önceki örnekteki*Günlükler* ), günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-282">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="5bacc-283">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="5bacc-283">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5bacc-284">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-284">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5bacc-285">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="5bacc-285">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5bacc-286">HATA</span><span class="sxs-lookup"><span data-stu-id="5bacc-286">ERROR</span></span>
* <span data-ttu-id="5bacc-287">UYARI</span><span class="sxs-lookup"><span data-stu-id="5bacc-287">WARNING</span></span>
* <span data-ttu-id="5bacc-288">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="5bacc-288">INFO</span></span>
* <span data-ttu-id="5bacc-289">TRACE</span><span class="sxs-lookup"><span data-stu-id="5bacc-289">TRACE</span></span>

<span data-ttu-id="5bacc-290">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="5bacc-290">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5bacc-291">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="5bacc-291">CONSOLE</span></span>
* <span data-ttu-id="5bacc-292">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="5bacc-292">EVENTLOG</span></span>
* <span data-ttu-id="5bacc-293">DOSYA</span><span class="sxs-lookup"><span data-stu-id="5bacc-293">FILE</span></span>

<span data-ttu-id="5bacc-294">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-294">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5bacc-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="5bacc-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="5bacc-296">(Varsayılan: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="5bacc-296">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5bacc-297">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="5bacc-297">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5bacc-298">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="5bacc-298">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5bacc-299">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="5bacc-299">The size of the log isn't limited.</span></span> <span data-ttu-id="5bacc-300">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-300">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="5bacc-301">web.configdosyasındaki öğenin bir örneği için [web.configyapılandırma](#configuration-with-webconfig) konusuna bakın `aspNetCore` . *web.config*</span><span class="sxs-lookup"><span data-stu-id="5bacc-301">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="5bacc-302">Yığın boyutunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="5bacc-302">Modify the stack size</span></span>

<span data-ttu-id="5bacc-303">*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="5bacc-303">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="5bacc-304">Yönetilen yığın boyutunu `stackSize` *web.config*bayt cinsinden ayarla ayarını kullanarak yapılandırın. Varsayılan boyut bayt 'tır `1048576` (1 MB).</span><span class="sxs-lookup"><span data-stu-id="5bacc-304">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5bacc-305">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="5bacc-305">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5bacc-306">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="5bacc-306">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="5bacc-307">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-307">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5bacc-308">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-308">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5bacc-309">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-309">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5bacc-310">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-310">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5bacc-311">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-311">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5bacc-312">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-312">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5bacc-313">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-313">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5bacc-314">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-314">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5bacc-315">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-315">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5bacc-316">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="5bacc-316">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5bacc-317">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-317">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5bacc-318">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-318">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5bacc-319">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="5bacc-319">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5bacc-320">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5bacc-320">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5bacc-321">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-321">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5bacc-322">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-322">Run the installer.</span></span>
1. <span data-ttu-id="5bacc-323">Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-323">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5bacc-324">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-324">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5bacc-325">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-325">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5bacc-326">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="5bacc-326">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5bacc-327">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-327">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5bacc-328">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="5bacc-328">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5bacc-329">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-329">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5bacc-330">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-330">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5bacc-331">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-331">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5bacc-332">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="5bacc-332">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5bacc-333">Modül</span><span class="sxs-lookup"><span data-stu-id="5bacc-333">Module</span></span>

<span data-ttu-id="5bacc-334">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="5bacc-334">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5bacc-335">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-335">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-336">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5bacc-338">% ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="5bacc-339">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="5bacc-339">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5bacc-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-341">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5bacc-343">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5bacc-344">Şema</span><span class="sxs-lookup"><span data-stu-id="5bacc-344">Schema</span></span>

<span data-ttu-id="5bacc-345">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5bacc-345">**IIS**</span></span>

* <span data-ttu-id="5bacc-346">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5bacc-347">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="5bacc-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5bacc-348">**IIS Express**</span></span>

* <span data-ttu-id="5bacc-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5bacc-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5bacc-351">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5bacc-351">Configuration</span></span>

<span data-ttu-id="5bacc-352">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5bacc-352">**IIS**</span></span>

* <span data-ttu-id="5bacc-353">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-353">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5bacc-354">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5bacc-354">**IIS Express**</span></span>

* <span data-ttu-id="5bacc-355">Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5bacc-356">*iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5bacc-357">Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-357">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5bacc-358">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="5bacc-358">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5bacc-359">`w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-359">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5bacc-360">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-360">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5bacc-361">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="5bacc-361">Supported Windows versions:</span></span>

* <span data-ttu-id="5bacc-362">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5bacc-362">Windows 7 or later</span></span>
* <span data-ttu-id="5bacc-363">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="5bacc-363">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5bacc-364">İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-364">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5bacc-365">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-365">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5bacc-366">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="5bacc-366">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5bacc-367">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-367">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5bacc-368">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="5bacc-368">In-process hosting model</span></span>

<span data-ttu-id="5bacc-369">Uygulamayı işlem içi barındırma için yapılandırmak için, `<AspNetCoreHostingModel>` özelliği uygulamanın proje dosyasına `InProcess` (işlem dışı barındırma ile ayarlanır `OutOfProcess` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bacc-369">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5bacc-370">İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-370">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="5bacc-371">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-371">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5bacc-372">`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-372">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="5bacc-373">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-373">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5bacc-374">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="5bacc-374">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5bacc-375">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="5bacc-375">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5bacc-376">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-376">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5bacc-377">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-377">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5bacc-378">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-378">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5bacc-379">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-379">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5bacc-380">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-380">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5bacc-381">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-381">Use one app pool per app.</span></span>

* <span data-ttu-id="5bacc-382">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtıma el ile bir [app_offline.htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-382">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5bacc-383">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-383">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5bacc-384">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-384">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5bacc-385">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="5bacc-385">Client disconnects are detected.</span></span> <span data-ttu-id="5bacc-386">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-386">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5bacc-387">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *w3wp.exe*için *c:\Windows\System32\inetsrv* ).</span><span class="sxs-lookup"><span data-stu-id="5bacc-387">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5bacc-388">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="5bacc-388">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5bacc-389">Yöntemini çağırın `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-389">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5bacc-390"><xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-390">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5bacc-391">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-391">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5bacc-392">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-392">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5bacc-393">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="5bacc-393">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5bacc-394">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="5bacc-394">Out-of-process hosting model</span></span>

<span data-ttu-id="5bacc-395">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, proje dosyasında aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5bacc-395">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="5bacc-396">`<AspNetCoreHostingModel>`Özelliği belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-396">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="5bacc-397">`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-397">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="5bacc-398">`<AspNetCoreHostingModel>`Özelliğin değerini olarak ayarlayın `OutOfProcess` (işlem içi barındırma ile ayarlanır `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="5bacc-398">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5bacc-399">Değer büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-399">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5bacc-400">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-400">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5bacc-401">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-401">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5bacc-402">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-402">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5bacc-403">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-403">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5bacc-404">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-404">Hosting model changes</span></span>

<span data-ttu-id="5bacc-405">Ayar, `hostingModel` *web.config* dosyasında değiştirilirse ( [web.configile yapılandırma](#configuration-with-webconfig) bölümünde açıklanan), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="5bacc-405">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5bacc-406">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-406">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5bacc-407">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-407">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5bacc-408">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="5bacc-408">Process name</span></span>

<span data-ttu-id="5bacc-409">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="5bacc-409">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="5bacc-410">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-410">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5bacc-411">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="5bacc-411">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5bacc-412">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-412">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5bacc-413">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-413">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5bacc-414">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-414">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5bacc-415">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="5bacc-415">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5bacc-416">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="5bacc-416">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5bacc-417">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5bacc-417">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5bacc-418">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5bacc-418">Configuration with web.config</span></span>

<span data-ttu-id="5bacc-419">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-419">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5bacc-420">Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-420">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5bacc-421">Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-421">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5bacc-422"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-422">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="5bacc-423">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-423">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5bacc-424">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-424">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5bacc-425">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="5bacc-425">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5bacc-426">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-426">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5bacc-427">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="5bacc-427">Attribute</span></span> | <span data-ttu-id="5bacc-428">Açıklama</span><span class="sxs-lookup"><span data-stu-id="5bacc-428">Description</span></span> | <span data-ttu-id="5bacc-429">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="5bacc-429">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5bacc-430">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-430">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-431">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-431">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5bacc-432">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-432">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-433">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-433">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5bacc-434">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-434">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-435">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-435">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5bacc-436">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-436">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="5bacc-437">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-437">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-438">Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-438">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="5bacc-439">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-439">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-440">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-440">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5bacc-441">&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-441">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5bacc-442">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-442">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5bacc-443">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="5bacc-443">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5bacc-444">Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="5bacc-444">Default: `1`</span></span><br><span data-ttu-id="5bacc-445">Min`1`</span><span class="sxs-lookup"><span data-stu-id="5bacc-445">Min: `1`</span></span><br><span data-ttu-id="5bacc-446">Biçimlendir`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="5bacc-446">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="5bacc-447">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-447">Required string attribute.</span></span></p><p><span data-ttu-id="5bacc-448">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="5bacc-448">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5bacc-449">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-449">Relative paths are supported.</span></span> <span data-ttu-id="5bacc-450">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-450">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5bacc-451">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-451">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-452">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-452">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5bacc-453">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="5bacc-453">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5bacc-454">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-454">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="5bacc-455">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="5bacc-455">Default: `10`</span></span><br><span data-ttu-id="5bacc-456">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-456">Min: `0`</span></span><br><span data-ttu-id="5bacc-457">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="5bacc-457">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5bacc-458">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-458">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5bacc-459">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-459">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5bacc-460">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-460">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5bacc-461">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-461">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5bacc-462">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-462">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="5bacc-463">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-463">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="5bacc-464">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-464">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="5bacc-465">Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-465">Default: `00:02:00`</span></span><br><span data-ttu-id="5bacc-466">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-466">Min: `00:00:00`</span></span><br><span data-ttu-id="5bacc-467">Biçimlendir`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-467">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5bacc-468">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-468">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-469">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="5bacc-469">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5bacc-470">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="5bacc-470">Default: `10`</span></span><br><span data-ttu-id="5bacc-471">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-471">Min: `0`</span></span><br><span data-ttu-id="5bacc-472">Biçimlendir`600`</span><span class="sxs-lookup"><span data-stu-id="5bacc-472">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5bacc-473">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-474">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="5bacc-474">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5bacc-475">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="5bacc-475">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="5bacc-476">*İşlem içi*barındırma sırasında: **işlem yeniden başlatılmaz** ve **rapidFailsPerMinute** ayarını **kullanmaz.**</span><span class="sxs-lookup"><span data-stu-id="5bacc-476">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="5bacc-477">*İşlem dışı*barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** sayısını başlatamadıkça sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="5bacc-477">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5bacc-478">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="5bacc-478">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5bacc-479">Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="5bacc-479">Default: `120`</span></span><br><span data-ttu-id="5bacc-480">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-480">Min: `0`</span></span><br><span data-ttu-id="5bacc-481">Biçimlendir`3600`</span><span class="sxs-lookup"><span data-stu-id="5bacc-481">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5bacc-482">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-482">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-483">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-483">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5bacc-484">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-484">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-485">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-485">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5bacc-486">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-486">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5bacc-487">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-487">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5bacc-488">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-488">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5bacc-489">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-489">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5bacc-490">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-490">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="5bacc-491">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="5bacc-491">Setting environment variables</span></span>

<span data-ttu-id="5bacc-492">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-492">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5bacc-493">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-493">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5bacc-494">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-494">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="5bacc-495">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-495">The following example sets two environment variables.</span></span> <span data-ttu-id="5bacc-496">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-496">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5bacc-497">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-497">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5bacc-498">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-498">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5bacc-499">Ortamı doğrudan *web.config* olarak ayarlamanın alternatifi, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-499">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="5bacc-500">Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5bacc-500">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="5bacc-501">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-501">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5bacc-502">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5bacc-502">app_offline.htm</span></span>

<span data-ttu-id="5bacc-503">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-503">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5bacc-504">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-504">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5bacc-505">*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-505">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5bacc-506">*app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-506">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="5bacc-507">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-507">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5bacc-508">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-508">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5bacc-509">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="5bacc-509">Start-up error page</span></span>

<span data-ttu-id="5bacc-510">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-510">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5bacc-511">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-511">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5bacc-512">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-512">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5bacc-513">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-513">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5bacc-514">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-514">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5bacc-515">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="5bacc-515">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5bacc-516">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="5bacc-516">Log creation and redirection</span></span>

<span data-ttu-id="5bacc-517">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-517">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5bacc-518">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-518">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5bacc-519">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="5bacc-519">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5bacc-520">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-520">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5bacc-521">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-521">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5bacc-522">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-522">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5bacc-523">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-523">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5bacc-524">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-524">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5bacc-525">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5bacc-525">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5bacc-526">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-526">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5bacc-527">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-527">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5bacc-528">`stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-528">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5bacc-529">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-529">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5bacc-530">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-530">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="5bacc-531">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-531">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5bacc-532">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-532">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="5bacc-533">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-533">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5bacc-534">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-534">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5bacc-535">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="5bacc-535">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5bacc-536">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-536">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5bacc-537">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-537">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5bacc-538">`<handlerSettings>`Öğesini `<aspNetCore>` *web.config*öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="5bacc-538">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5bacc-539">Değer için belirtilen yoldaki klasörler `<handlerSetting>` (önceki örnekteki*Günlükler* ) modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-539">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="5bacc-540">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="5bacc-540">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5bacc-541">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-541">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5bacc-542">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="5bacc-542">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5bacc-543">HATA</span><span class="sxs-lookup"><span data-stu-id="5bacc-543">ERROR</span></span>
* <span data-ttu-id="5bacc-544">UYARI</span><span class="sxs-lookup"><span data-stu-id="5bacc-544">WARNING</span></span>
* <span data-ttu-id="5bacc-545">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="5bacc-545">INFO</span></span>
* <span data-ttu-id="5bacc-546">TRACE</span><span class="sxs-lookup"><span data-stu-id="5bacc-546">TRACE</span></span>

<span data-ttu-id="5bacc-547">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="5bacc-547">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5bacc-548">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="5bacc-548">CONSOLE</span></span>
* <span data-ttu-id="5bacc-549">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="5bacc-549">EVENTLOG</span></span>
* <span data-ttu-id="5bacc-550">DOSYA</span><span class="sxs-lookup"><span data-stu-id="5bacc-550">FILE</span></span>

<span data-ttu-id="5bacc-551">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-551">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5bacc-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="5bacc-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="5bacc-553">(Varsayılan: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="5bacc-553">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5bacc-554">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="5bacc-554">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5bacc-555">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="5bacc-555">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5bacc-556">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="5bacc-556">The size of the log isn't limited.</span></span> <span data-ttu-id="5bacc-557">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-557">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="5bacc-558">web.configdosyasındaki öğenin bir örneği için [web.configyapılandırma](#configuration-with-webconfig) konusuna bakın `aspNetCore` . *web.config*</span><span class="sxs-lookup"><span data-stu-id="5bacc-558">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5bacc-559">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="5bacc-559">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5bacc-560">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="5bacc-560">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="5bacc-561">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-561">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5bacc-562">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-562">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5bacc-563">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-563">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5bacc-564">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-564">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5bacc-565">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-565">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5bacc-566">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-566">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5bacc-567">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-567">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5bacc-568">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-568">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5bacc-569">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-569">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5bacc-570">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="5bacc-570">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5bacc-571">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-571">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5bacc-572">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-572">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5bacc-573">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="5bacc-573">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5bacc-574">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5bacc-574">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5bacc-575">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-575">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5bacc-576">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-576">Run the installer.</span></span>
1. <span data-ttu-id="5bacc-577">Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-577">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5bacc-578">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-578">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5bacc-579">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-579">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5bacc-580">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="5bacc-580">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5bacc-581">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-581">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5bacc-582">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="5bacc-582">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5bacc-583">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-583">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5bacc-584">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-584">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5bacc-585">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-585">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5bacc-586">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="5bacc-586">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5bacc-587">Modül</span><span class="sxs-lookup"><span data-stu-id="5bacc-587">Module</span></span>

<span data-ttu-id="5bacc-588">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="5bacc-588">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5bacc-589">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-589">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-590">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5bacc-592">% ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="5bacc-593">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="5bacc-593">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5bacc-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-595">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5bacc-597">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5bacc-598">Şema</span><span class="sxs-lookup"><span data-stu-id="5bacc-598">Schema</span></span>

<span data-ttu-id="5bacc-599">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5bacc-599">**IIS**</span></span>

* <span data-ttu-id="5bacc-600">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5bacc-601">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="5bacc-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5bacc-602">**IIS Express**</span></span>

* <span data-ttu-id="5bacc-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5bacc-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5bacc-605">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5bacc-605">Configuration</span></span>

<span data-ttu-id="5bacc-606">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5bacc-606">**IIS**</span></span>

* <span data-ttu-id="5bacc-607">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-607">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5bacc-608">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5bacc-608">**IIS Express**</span></span>

* <span data-ttu-id="5bacc-609">Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5bacc-610">*iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5bacc-611">Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-611">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5bacc-612">ASP.NET Core modülü, Web isteklerini arka uca ASP.NET Core uygulamalarına iletmek için IIS ardışık düzenine takılan yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="5bacc-612">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="5bacc-613">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="5bacc-613">Supported Windows versions:</span></span>

* <span data-ttu-id="5bacc-614">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5bacc-614">Windows 7 or later</span></span>
* <span data-ttu-id="5bacc-615">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="5bacc-615">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5bacc-616">Modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-616">The module only works with Kestrel.</span></span> <span data-ttu-id="5bacc-617">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)uyumsuz.</span><span class="sxs-lookup"><span data-stu-id="5bacc-617">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="5bacc-618">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini de işler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-618">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="5bacc-619">Modül, ilk istek ulaştığında ASP.NET Core App işlemini başlatır ve kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-619">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="5bacc-620">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS 'de işlem içinde çalışan ASP.NET 4. x uygulamaları ile görüldüğü aynı davranıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-620">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5bacc-621">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-621">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="5bacc-623">İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-623">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5bacc-624">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5bacc-624">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5bacc-625">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-625">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5bacc-626">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-626">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5bacc-627">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-627">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5bacc-628">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-628">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5bacc-629">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-629">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5bacc-630">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-630">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5bacc-631">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-631">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5bacc-632">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-632">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5bacc-633">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-633">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5bacc-634">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="5bacc-634">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5bacc-635">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="5bacc-635">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5bacc-636">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-636">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5bacc-637">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-637">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5bacc-638">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="5bacc-638">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5bacc-639">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="5bacc-639">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5bacc-640">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5bacc-640">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5bacc-641">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5bacc-641">Configuration with web.config</span></span>

<span data-ttu-id="5bacc-642">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-642">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5bacc-643">Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-643">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5bacc-644">Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="5bacc-644">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5bacc-645">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-645">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5bacc-646">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-646">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5bacc-647">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="5bacc-647">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5bacc-648">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-648">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5bacc-649">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="5bacc-649">Attribute</span></span> | <span data-ttu-id="5bacc-650">Açıklama</span><span class="sxs-lookup"><span data-stu-id="5bacc-650">Description</span></span> | <span data-ttu-id="5bacc-651">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="5bacc-651">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5bacc-652">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-652">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-653">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-653">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5bacc-654">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-655">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-655">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5bacc-656">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-656">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-657">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-657">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5bacc-658">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-658">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="5bacc-659">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-660">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-660">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5bacc-661">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-661">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5bacc-662">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="5bacc-662">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5bacc-663">Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="5bacc-663">Default: `1`</span></span><br><span data-ttu-id="5bacc-664">Min`1`</span><span class="sxs-lookup"><span data-stu-id="5bacc-664">Min: `1`</span></span><br><span data-ttu-id="5bacc-665">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="5bacc-665">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="5bacc-666">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-666">Required string attribute.</span></span></p><p><span data-ttu-id="5bacc-667">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="5bacc-667">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5bacc-668">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-668">Relative paths are supported.</span></span> <span data-ttu-id="5bacc-669">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-669">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5bacc-670">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-670">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-671">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-671">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5bacc-672">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="5bacc-672">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="5bacc-673">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="5bacc-673">Default: `10`</span></span><br><span data-ttu-id="5bacc-674">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-674">Min: `0`</span></span><br><span data-ttu-id="5bacc-675">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="5bacc-675">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5bacc-676">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-676">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5bacc-677">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-677">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5bacc-678">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-678">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="5bacc-679">Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-679">Default: `00:02:00`</span></span><br><span data-ttu-id="5bacc-680">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-680">Min: `00:00:00`</span></span><br><span data-ttu-id="5bacc-681">Biçimlendir`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5bacc-681">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5bacc-682">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-682">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-683">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="5bacc-683">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5bacc-684">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="5bacc-684">Default: `10`</span></span><br><span data-ttu-id="5bacc-685">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-685">Min: `0`</span></span><br><span data-ttu-id="5bacc-686">Biçimlendir`600`</span><span class="sxs-lookup"><span data-stu-id="5bacc-686">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5bacc-687">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-687">Optional integer attribute.</span></span></p><p><span data-ttu-id="5bacc-688">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="5bacc-688">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5bacc-689">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="5bacc-689">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5bacc-690">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="5bacc-690">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5bacc-691">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="5bacc-691">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5bacc-692">Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="5bacc-692">Default: `120`</span></span><br><span data-ttu-id="5bacc-693">Min`0`</span><span class="sxs-lookup"><span data-stu-id="5bacc-693">Min: `0`</span></span><br><span data-ttu-id="5bacc-694">Biçimlendir`3600`</span><span class="sxs-lookup"><span data-stu-id="5bacc-694">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5bacc-695">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-695">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5bacc-696">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-696">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5bacc-697">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="5bacc-697">Optional string attribute.</span></span></p><p><span data-ttu-id="5bacc-698">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-698">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5bacc-699">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-699">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5bacc-700">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-700">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5bacc-701">Modülün günlük dosyasını oluşturması için yolda sunulan klasörlerin bulunması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-701">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="5bacc-702">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-702">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5bacc-703">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-703">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="5bacc-704">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="5bacc-704">Setting environment variables</span></span>

<span data-ttu-id="5bacc-705">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-705">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5bacc-706">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-706">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="5bacc-707">Bu bölümde ayarlanan ortam değişkenleri, aynı ada sahip sistem ortam değişkenleri ile çakışıyor.</span><span class="sxs-lookup"><span data-stu-id="5bacc-707">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="5bacc-708">Bir ortam değişkeni hem *web.config* dosyasında hem de Windows 'un sistem düzeyinde ayarlandıysa, *web.config* dosyasındaki değer, uygulamanın başlamasını önleyen sistem ortam değişkeni değerine (örneğin, `ASPNETCORE_ENVIRONMENT: Development;Development` ) eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-708">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="5bacc-709">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-709">The following example sets two environment variables.</span></span> <span data-ttu-id="5bacc-710">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-710">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5bacc-711">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-711">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5bacc-712">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-712">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5bacc-713">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-713">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5bacc-714">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5bacc-714">app_offline.htm</span></span>

<span data-ttu-id="5bacc-715">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-715">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5bacc-716">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-716">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5bacc-717">*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-717">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5bacc-718">*app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-718">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5bacc-719">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="5bacc-719">Start-up error page</span></span>

<span data-ttu-id="5bacc-720">ASP.NET Core modülü arka uç işlemini başlatamaz veya arka uç işlemi başlatılır, ancak yapılandırılmış bağlantı noktasında dinleme başarısız olursa, *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-720">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="5bacc-721">Bu sayfayı bastırın ve varsayılan IIS 502 durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-721">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5bacc-722">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="5bacc-722">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502,5 işlem hatası durum kodu sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5bacc-724">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="5bacc-724">Log creation and redirection</span></span>

<span data-ttu-id="5bacc-725">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-725">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5bacc-726">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-726">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5bacc-727">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="5bacc-727">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5bacc-728">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-728">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5bacc-729">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-729">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5bacc-730">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-730">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5bacc-731">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-731">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5bacc-732">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-732">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5bacc-733">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5bacc-733">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5bacc-734">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-734">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5bacc-735">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-735">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5bacc-736">`stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-736">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5bacc-737">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-737">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5bacc-738">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-738">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="5bacc-739">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-739">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5bacc-740">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-740">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5bacc-741">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-741">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="5bacc-742">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="5bacc-742">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5bacc-743">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="5bacc-743">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5bacc-744">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-744">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5bacc-745">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="5bacc-745">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5bacc-746">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-746">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5bacc-747">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="5bacc-747">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5bacc-748">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-748">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5bacc-749">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="5bacc-749">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5bacc-750">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-750">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5bacc-751">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-751">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5bacc-752">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="5bacc-752">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5bacc-753">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="5bacc-753">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5bacc-754">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-754">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5bacc-755">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="5bacc-755">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5bacc-756">IIS paylaşılan yapılandırması kullanırken, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5bacc-756">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="5bacc-757">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-757">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5bacc-758">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-758">Run the installer.</span></span>
1. <span data-ttu-id="5bacc-759">Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="5bacc-759">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5bacc-760">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-760">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5bacc-761">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="5bacc-761">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5bacc-762">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="5bacc-762">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5bacc-763">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-763">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5bacc-764">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="5bacc-764">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5bacc-765">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5bacc-765">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5bacc-766">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5bacc-766">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5bacc-767">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bacc-767">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5bacc-768">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="5bacc-768">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5bacc-769">Modül</span><span class="sxs-lookup"><span data-stu-id="5bacc-769">Module</span></span>

<span data-ttu-id="5bacc-770">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="5bacc-770">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5bacc-771">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-771">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-772">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="5bacc-773">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="5bacc-773">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5bacc-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5bacc-775">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5bacc-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5bacc-776">Şema</span><span class="sxs-lookup"><span data-stu-id="5bacc-776">Schema</span></span>

<span data-ttu-id="5bacc-777">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5bacc-777">**IIS**</span></span>

* <span data-ttu-id="5bacc-778">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="5bacc-779">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5bacc-779">**IIS Express**</span></span>

* <span data-ttu-id="5bacc-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5bacc-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5bacc-781">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5bacc-781">Configuration</span></span>

<span data-ttu-id="5bacc-782">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5bacc-782">**IIS**</span></span>

* <span data-ttu-id="5bacc-783">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-783">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5bacc-784">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5bacc-784">**IIS Express**</span></span>

* <span data-ttu-id="5bacc-785">Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5bacc-786">*iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5bacc-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5bacc-787">Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5bacc-787">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5bacc-788">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5bacc-788">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="5bacc-789">[ASP.NET Core modülü başvuru kaynağı (ana dal)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): belirli bir sürümü seçmek için **dal** açılan listesini kullanın (örneğin, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="5bacc-789">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
