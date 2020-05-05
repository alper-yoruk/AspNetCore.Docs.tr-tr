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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c9cc340c9a3d6ff1b3783686bd779dfe04b14275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775277"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="28633-103">ASP.NET Core Modülü</span><span class="sxs-lookup"><span data-stu-id="28633-103">ASP.NET Core Module</span></span>

<span data-ttu-id="28633-104">, [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris](https://github.com/Tratcher)No, [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)ve ka [kotalık](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="28633-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="28633-105">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="28633-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="28633-106">`w3wp.exe` [İşlem içi barındırma MODELI](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="28633-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="28633-107">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="28633-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="28633-108">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="28633-108">Supported Windows versions:</span></span>

* <span data-ttu-id="28633-109">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="28633-109">Windows 7 or later</span></span>
* <span data-ttu-id="28633-110">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="28633-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="28633-111">İşlem içinde barındırırken, modül IIS HTTP sunucusu (`IISHttpServer`) olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="28633-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="28633-112">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="28633-113">Modül, [http. sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="28633-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="28633-114">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="28633-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="28633-115">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="28633-115">In-process hosting model</span></span>

<span data-ttu-id="28633-116">Uygulamalar, işlem içi barındırma modelinde varsayılan olarak ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28633-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="28633-117">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="28633-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="28633-118">Kestrel Server yerine IIS`IISHttpServer`http sunucusu () kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="28633-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="28633-119">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="28633-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="28633-120">Kaydolun `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="28633-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="28633-121">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="28633-122">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="28633-123">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="28633-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="28633-124">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="28633-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="28633-125">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-125">Use one app pool per app.</span></span>

* <span data-ttu-id="28633-126">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtımda el ile bir [app_offline. htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="28633-127">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="28633-128">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="28633-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="28633-129">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="28633-129">Client disconnects are detected.</span></span> <span data-ttu-id="28633-130">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="28633-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="28633-131">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın DIZINI yerine IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *c:\Windows\System32\inetsrv* için, *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="28633-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="28633-132">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="28633-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="28633-133">`SetCurrentDirectory` Yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="28633-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="28633-134">Uygulamanın dizinini sağlamak <xref:System.IO.Directory.GetCurrentDirectory*> için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="28633-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="28633-135">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="28633-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="28633-136">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="28633-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="28633-137">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, kimlik doğrulama hizmetleri <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="28633-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="28633-138">[Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="28633-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="28633-139">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="28633-139">Out-of-process hosting model</span></span>

<span data-ttu-id="28633-140">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini proje dosyasında ( `OutOfProcess` *. csproj*) olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="28633-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="28633-141">İşlem içi barındırma, varsayılan değer olan `InProcess`ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="28633-142">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="28633-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="28633-143">[Kestrel](xref:fundamentals/servers/kestrel) IIS HTTP Server (`IISHttpServer`) yerine Kestrel Server kullanılır.</span><span class="sxs-lookup"><span data-stu-id="28633-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="28633-144">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="28633-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="28633-145">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="28633-146">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="28633-147">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="28633-147">Hosting model changes</span></span>

<span data-ttu-id="28633-148">`hostingModel` Ayar *Web. config* dosyasında değiştirilirse ( [Web. config ile yapılandırma](#configuration-with-webconfig) bölümünde açıklanmıştır), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="28633-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="28633-149">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="28633-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="28633-150">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="28633-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="28633-151">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="28633-151">Process name</span></span>

<span data-ttu-id="28633-152">`Process.GetCurrentProcess().ProcessName``w3wp` / raporlar `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="28633-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="28633-153">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="28633-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="28633-154">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules>..</span><span class="sxs-lookup"><span data-stu-id="28633-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="28633-155">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="28633-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="28633-156">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28633-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="28633-157">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="28633-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="28633-158">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="28633-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="28633-159">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="28633-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="28633-160">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="28633-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="28633-161">Web. config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28633-161">Configuration with web.config</span></span>

<span data-ttu-id="28633-162">ASP.NET Core modülü, sitenin *Web. config* dosyasındaki `aspNetCore` `system.webServer` düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="28633-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="28633-163">Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="28633-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="28633-164">Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="28633-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="28633-165"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Özelliği `false` [, \<konum>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="28633-166">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak `\\?\%home%\LogFiles\stdout`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="28633-167">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="28633-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="28633-168">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications>..</span><span class="sxs-lookup"><span data-stu-id="28633-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="28633-169">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="28633-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="28633-170">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="28633-170">Attribute</span></span> | <span data-ttu-id="28633-171">Açıklama</span><span class="sxs-lookup"><span data-stu-id="28633-171">Description</span></span> | <span data-ttu-id="28633-172">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="28633-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="28633-173">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-173">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-174">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="28633-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="28633-175">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-176">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="28633-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="28633-177">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-178">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="28633-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="28633-179">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="28633-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="28633-180">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-180">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-181">Barındırma`InProcess`/`inprocess`modelini işlem içi () veya işlem dışı (`OutOfProcess`/`outofprocess`) olarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="28633-182">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-183">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="28633-184">&dagger;İşlem içi barındırma için, değer ile `1`sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="28633-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="28633-185">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="28633-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="28633-186">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="28633-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="28633-187">Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="28633-187">Default: `1`</span></span><br><span data-ttu-id="28633-188">Min`1`</span><span class="sxs-lookup"><span data-stu-id="28633-188">Min: `1`</span></span><br><span data-ttu-id="28633-189">Biçimlendir`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="28633-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="28633-190">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-190">Required string attribute.</span></span></p><p><span data-ttu-id="28633-191">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="28633-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="28633-192">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-192">Relative paths are supported.</span></span> <span data-ttu-id="28633-193">Yol ile `.`başlıyorsa, yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="28633-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="28633-194">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-195">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="28633-196">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="28633-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="28633-197">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="28633-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="28633-198">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="28633-198">Default: `10`</span></span><br><span data-ttu-id="28633-199">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-199">Min: `0`</span></span><br><span data-ttu-id="28633-200">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="28633-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="28633-201">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="28633-202">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="28633-203">ASP.NET Core 2,1 veya üzeri `requestTimeout` sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="28633-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="28633-204">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="28633-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="28633-205">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="28633-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="28633-206">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="28633-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="28633-207">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</span><span class="sxs-lookup"><span data-stu-id="28633-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="28633-208">Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="28633-208">Default: `00:02:00`</span></span><br><span data-ttu-id="28633-209">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="28633-209">Min: `00:00:00`</span></span><br><span data-ttu-id="28633-210">Biçimlendir`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="28633-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="28633-211">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-212">*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="28633-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="28633-213">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="28633-213">Default: `10`</span></span><br><span data-ttu-id="28633-214">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-214">Min: `0`</span></span><br><span data-ttu-id="28633-215">Biçimlendir`600`</span><span class="sxs-lookup"><span data-stu-id="28633-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="28633-216">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-217">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="28633-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="28633-218">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="28633-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="28633-219">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="28633-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="28633-220">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="28633-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="28633-221">Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="28633-221">Default: `120`</span></span><br><span data-ttu-id="28633-222">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-222">Min: `0`</span></span><br><span data-ttu-id="28633-223">Biçimlendir`3600`</span><span class="sxs-lookup"><span data-stu-id="28633-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="28633-224">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-225">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="28633-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="28633-226">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-226">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-227">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="28633-228">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="28633-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="28633-229">İle `.` başlayan tüm yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="28633-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="28633-230">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28633-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="28633-231">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="28633-232">Değer `.\logs\stdout` olarak sağlandıysa, bir 2/5/2018 Işlem 1934 kimliği ile 19:41:32 ' de ' *de kaydedildiğinde günlük* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="28633-233">Ortam değişkenlerini belirleme</span><span class="sxs-lookup"><span data-stu-id="28633-233">Set environment variables</span></span>

<span data-ttu-id="28633-234">`processPath` Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="28633-235">Bir `<environmentVariable>` `<environmentVariables>` koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin.</span><span class="sxs-lookup"><span data-stu-id="28633-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="28633-236">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="28633-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="28633-237">Aşağıdaki örnek, *Web. config*dosyasında iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT` uygulamanın ortamını olarak `Development`yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="28633-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="28633-238">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="28633-239">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="28633-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="28633-240">Ortamı doğrudan *Web. config* içinde ayarlamaya alternatif olarak, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil etmek de vardır.</span><span class="sxs-lookup"><span data-stu-id="28633-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="28633-241">Bu yaklaşım, proje yayımlandığında *Web. config* içinde ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="28633-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="28633-242">`ASPNETCORE_ENVIRONMENT` Ortam değişkenini yalnızca, Internet gibi `Development` güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28633-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="28633-243">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="28633-243">app_offline.htm</span></span>

<span data-ttu-id="28633-244">Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="28633-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="28633-245">Uygulama, içinde `shutdownTimeLimit`tanımlanan saniye sayısından sonra hala çalışıyorsa, ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="28633-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="28633-246">*App_offline. htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline. htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="28633-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="28633-247">*App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="28633-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="28633-248">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="28633-249">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="28633-250">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="28633-250">Start-up error page</span></span>

<span data-ttu-id="28633-251">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="28633-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="28633-252">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="28633-253">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="28633-254">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="28633-255">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="28633-256">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<hataları HttpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="28633-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="28633-257">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="28633-257">Log creation and redirection</span></span>

<span data-ttu-id="28633-258">ASP.NET Core modülü, `stdoutLogEnabled` `aspNetCore` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden `stdoutLogFile` yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="28633-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="28633-259">`stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28633-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="28633-260">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için kullanın `IIS AppPool\<app_pool_name>` ).</span><span class="sxs-lookup"><span data-stu-id="28633-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="28633-261">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="28633-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="28633-262">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="28633-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="28633-263">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="28633-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="28633-264">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="28633-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="28633-265">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="28633-266">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="28633-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="28633-267">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="28633-268">Günlük dosyası adı, alt çizgi ile ayrılmış `stdoutLogFile` yolun (genellikle *stdout*) son kesimine zaman damgası, işlem kimliği ve dosya uzantısı (*. log*) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="28633-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="28633-269">`stdoutLogFile` Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="28633-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="28633-270">Yanlış `stdoutLogEnabled` ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="28633-271">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="28633-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="28633-272">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki `.\log\`stdout günlüğünü yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="28633-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="28633-273">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="28633-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="28633-274">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak `\\?\%home%\LogFiles\stdout`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="28633-275">Ortam `%home` değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="28633-276">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="28633-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="28633-277">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="28633-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="28633-278">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="28633-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="28633-279">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="28633-280">`<handlerSettings>` Öğesini *Web. config*dosyasındaki `<aspNetCore>` öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="28633-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="28633-281">Yoldaki tüm klasörler (önceki örnekteki*Günlükler* ), günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28633-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="28633-282">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için kullanın `IIS AppPool\<app_pool_name>` ).</span><span class="sxs-lookup"><span data-stu-id="28633-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="28633-283">Hata ayıklama düzeyi`debugLevel`() değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="28633-284">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="28633-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="28633-285">HATA</span><span class="sxs-lookup"><span data-stu-id="28633-285">ERROR</span></span>
* <span data-ttu-id="28633-286">UYARI</span><span class="sxs-lookup"><span data-stu-id="28633-286">WARNING</span></span>
* <span data-ttu-id="28633-287">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="28633-287">INFO</span></span>
* <span data-ttu-id="28633-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="28633-288">TRACE</span></span>

<span data-ttu-id="28633-289">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="28633-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="28633-290">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="28633-290">CONSOLE</span></span>
* <span data-ttu-id="28633-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="28633-291">EVENTLOG</span></span>
* <span data-ttu-id="28633-292">DOSYA</span><span class="sxs-lookup"><span data-stu-id="28633-292">FILE</span></span>

<span data-ttu-id="28633-293">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="28633-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="28633-294">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="28633-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="28633-295">(Varsayılan: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="28633-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="28633-296">`ASPNETCORE_MODULE_DEBUG`&ndash; Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="28633-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="28633-297">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="28633-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="28633-298">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="28633-298">The size of the log isn't limited.</span></span> <span data-ttu-id="28633-299">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="28633-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="28633-300">*Web. config* dosyasındaki `aspNetCore` öğesinin bir örneği için bkz. [Web. config ile yapılandırma](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="28633-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="28633-301">Yığın boyutunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="28633-301">Modify the stack size</span></span>

<span data-ttu-id="28633-302">*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="28633-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="28633-303">Yönetilen yığın boyutunu, `stackSize` *Web. config*dosyasında bayt cinsinden ayarını kullanarak yapılandırın. Varsayılan boyut bayt 'tır `1048576` (1 MB).</span><span class="sxs-lookup"><span data-stu-id="28633-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="28633-304">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="28633-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="28633-305">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="28633-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="28633-306">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="28633-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="28633-307">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="28633-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="28633-308">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="28633-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="28633-309">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine (`ASPNETCORE_TOKEN`) ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="28633-310">Eşleştirme belirteci, her proxy istek için de bir üst`MS-ASPNETCORE-TOKEN`bilgi () olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="28633-311">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="28633-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="28633-312">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="28633-313">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="28633-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="28633-314">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="28633-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="28633-315">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="28633-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="28633-316">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="28633-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="28633-317">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="28633-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="28633-318">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde `OPT_NO_SHARED_CONFIG_CHECK` `1`ayarlanan parametre Ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="28633-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="28633-319">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="28633-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="28633-320">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="28633-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="28633-321">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="28633-321">Run the installer.</span></span>
1. <span data-ttu-id="28633-322">Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="28633-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="28633-323">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="28633-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="28633-324">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="28633-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="28633-325">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="28633-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="28633-326">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="28633-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="28633-327">*Aspnetcore. dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="28633-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="28633-328">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="28633-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="28633-329">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="28633-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="28633-330">Modülün barındırma paketi yükleyici günlükleri *C:\\Users\\% username%\\AppData\\local\\Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__\<zaman damgası>_000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="28633-331">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="28633-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="28633-332">Modül</span><span class="sxs-lookup"><span data-stu-id="28633-332">Module</span></span>

<span data-ttu-id="28633-333">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="28633-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="28633-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-336">%ProgramFiles%\IIS\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="28633-337">% ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="28633-338">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="28633-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="28633-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-340">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="28633-342">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="28633-343">Şema</span><span class="sxs-lookup"><span data-stu-id="28633-343">Schema</span></span>

<span data-ttu-id="28633-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="28633-344">**IIS**</span></span>

* <span data-ttu-id="28633-345">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="28633-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="28633-346">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="28633-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="28633-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="28633-347">**IIS Express**</span></span>

* <span data-ttu-id="28633-348">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="28633-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="28633-349">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="28633-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="28633-350">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28633-350">Configuration</span></span>

<span data-ttu-id="28633-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="28633-351">**IIS**</span></span>

* <span data-ttu-id="28633-352">%Windir%\System32\inetsrv\config\applicationHost,config</span><span class="sxs-lookup"><span data-stu-id="28633-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="28633-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="28633-353">**IIS Express**</span></span>

* <span data-ttu-id="28633-354">Visual Studio: {APPLICATION ROOT}\\. Vs\config\applicationhost,config</span><span class="sxs-lookup"><span data-stu-id="28633-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="28633-355">*ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="28633-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="28633-356">Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="28633-357">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="28633-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="28633-358">`w3wp.exe` [İşlem içi barındırma MODELI](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="28633-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="28633-359">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="28633-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="28633-360">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="28633-360">Supported Windows versions:</span></span>

* <span data-ttu-id="28633-361">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="28633-361">Windows 7 or later</span></span>
* <span data-ttu-id="28633-362">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="28633-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="28633-363">İşlem içinde barındırırken, modül IIS HTTP sunucusu (`IISHttpServer`) olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="28633-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="28633-364">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="28633-365">Modül, [http. sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="28633-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="28633-366">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="28633-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="28633-367">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="28633-367">In-process hosting model</span></span>

<span data-ttu-id="28633-368">Uygulamayı işlem içi barındırma için yapılandırmak için, `<AspNetCoreHostingModel>` özelliği uygulamanın proje dosyasına `InProcess` (işlem dışı barındırma ile `OutOfProcess`ayarlanır) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="28633-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="28633-369">İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="28633-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="28633-370">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="28633-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="28633-371">`<AspNetCoreHostingModel>` Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="28633-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="28633-372">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="28633-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="28633-373">Kestrel Server yerine IIS`IISHttpServer`http sunucusu () kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="28633-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="28633-374">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="28633-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="28633-375">Kaydolun `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="28633-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="28633-376">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="28633-377">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="28633-378">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="28633-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="28633-379">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="28633-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="28633-380">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-380">Use one app pool per app.</span></span>

* <span data-ttu-id="28633-381">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtımda el ile bir [app_offline. htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="28633-382">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="28633-383">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="28633-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="28633-384">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="28633-384">Client disconnects are detected.</span></span> <span data-ttu-id="28633-385">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="28633-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="28633-386">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın DIZINI yerine IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *c:\Windows\System32\inetsrv* için, *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="28633-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="28633-387">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="28633-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="28633-388">`SetCurrentDirectory` Yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="28633-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="28633-389">Uygulamanın dizinini sağlamak <xref:System.IO.Directory.GetCurrentDirectory*> için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="28633-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="28633-390">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="28633-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="28633-391">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="28633-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="28633-392">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, kimlik doğrulama hizmetleri <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="28633-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="28633-393">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="28633-393">Out-of-process hosting model</span></span>

<span data-ttu-id="28633-394">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, proje dosyasında aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="28633-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="28633-395">`<AspNetCoreHostingModel>` Özelliği belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="28633-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="28633-396">`<AspNetCoreHostingModel>` Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="28633-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="28633-397">`<AspNetCoreHostingModel>` Özelliğin değerini olarak `OutOfProcess` ayarlayın (işlem içi barındırma ile `InProcess`ayarlanır):</span><span class="sxs-lookup"><span data-stu-id="28633-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="28633-398">Değer büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="28633-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="28633-399">[Kestrel](xref:fundamentals/servers/kestrel) IIS HTTP Server (`IISHttpServer`) yerine Kestrel Server kullanılır.</span><span class="sxs-lookup"><span data-stu-id="28633-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="28633-400">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="28633-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="28633-401">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="28633-402">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="28633-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="28633-403">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="28633-403">Hosting model changes</span></span>

<span data-ttu-id="28633-404">`hostingModel` Ayar *Web. config* dosyasında değiştirilirse ( [Web. config ile yapılandırma](#configuration-with-webconfig) bölümünde açıklanmıştır), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="28633-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="28633-405">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="28633-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="28633-406">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="28633-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="28633-407">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="28633-407">Process name</span></span>

<span data-ttu-id="28633-408">`Process.GetCurrentProcess().ProcessName``w3wp` / raporlar `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="28633-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="28633-409">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="28633-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="28633-410">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules>..</span><span class="sxs-lookup"><span data-stu-id="28633-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="28633-411">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="28633-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="28633-412">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28633-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="28633-413">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="28633-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="28633-414">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="28633-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="28633-415">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="28633-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="28633-416">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="28633-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="28633-417">Web. config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28633-417">Configuration with web.config</span></span>

<span data-ttu-id="28633-418">ASP.NET Core modülü, sitenin *Web. config* dosyasındaki `aspNetCore` `system.webServer` düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="28633-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="28633-419">Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="28633-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="28633-420">Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="28633-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="28633-421"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Özelliği `false` [, \<konum>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="28633-422">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak `\\?\%home%\LogFiles\stdout`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="28633-423">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="28633-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="28633-424">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications>..</span><span class="sxs-lookup"><span data-stu-id="28633-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="28633-425">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="28633-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="28633-426">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="28633-426">Attribute</span></span> | <span data-ttu-id="28633-427">Açıklama</span><span class="sxs-lookup"><span data-stu-id="28633-427">Description</span></span> | <span data-ttu-id="28633-428">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="28633-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="28633-429">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-429">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-430">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="28633-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="28633-431">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-432">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="28633-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="28633-433">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-434">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="28633-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="28633-435">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="28633-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="28633-436">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-436">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-437">Barındırma`InProcess`/`inprocess`modelini işlem içi () veya işlem dışı (`OutOfProcess`/`outofprocess`) olarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="28633-438">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-439">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="28633-440">&dagger;İşlem içi barındırma için, değer ile `1`sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="28633-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="28633-441">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="28633-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="28633-442">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="28633-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="28633-443">Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="28633-443">Default: `1`</span></span><br><span data-ttu-id="28633-444">Min`1`</span><span class="sxs-lookup"><span data-stu-id="28633-444">Min: `1`</span></span><br><span data-ttu-id="28633-445">Biçimlendir`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="28633-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="28633-446">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-446">Required string attribute.</span></span></p><p><span data-ttu-id="28633-447">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="28633-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="28633-448">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-448">Relative paths are supported.</span></span> <span data-ttu-id="28633-449">Yol ile `.`başlıyorsa, yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="28633-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="28633-450">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-451">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="28633-452">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="28633-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="28633-453">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="28633-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="28633-454">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="28633-454">Default: `10`</span></span><br><span data-ttu-id="28633-455">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-455">Min: `0`</span></span><br><span data-ttu-id="28633-456">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="28633-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="28633-457">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="28633-458">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="28633-459">ASP.NET Core 2,1 veya üzeri `requestTimeout` sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="28633-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="28633-460">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="28633-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="28633-461">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="28633-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="28633-462">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="28633-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="28633-463">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</span><span class="sxs-lookup"><span data-stu-id="28633-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="28633-464">Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="28633-464">Default: `00:02:00`</span></span><br><span data-ttu-id="28633-465">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="28633-465">Min: `00:00:00`</span></span><br><span data-ttu-id="28633-466">Biçimlendir`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="28633-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="28633-467">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-468">*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="28633-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="28633-469">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="28633-469">Default: `10`</span></span><br><span data-ttu-id="28633-470">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-470">Min: `0`</span></span><br><span data-ttu-id="28633-471">Biçimlendir`600`</span><span class="sxs-lookup"><span data-stu-id="28633-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="28633-472">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-473">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="28633-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="28633-474">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="28633-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="28633-475">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="28633-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="28633-476">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="28633-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="28633-477">Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="28633-477">Default: `120`</span></span><br><span data-ttu-id="28633-478">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-478">Min: `0`</span></span><br><span data-ttu-id="28633-479">Biçimlendir`3600`</span><span class="sxs-lookup"><span data-stu-id="28633-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="28633-480">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-481">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="28633-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="28633-482">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-482">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-483">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="28633-484">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="28633-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="28633-485">İle `.` başlayan tüm yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="28633-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="28633-486">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28633-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="28633-487">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="28633-488">Değer `.\logs\stdout` olarak sağlandıysa, bir 2/5/2018 Işlem 1934 kimliği ile 19:41:32 ' de ' *de kaydedildiğinde günlük* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="28633-489">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="28633-489">Setting environment variables</span></span>

<span data-ttu-id="28633-490">`processPath` Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="28633-491">Bir `<environmentVariable>` `<environmentVariables>` koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin.</span><span class="sxs-lookup"><span data-stu-id="28633-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="28633-492">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="28633-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="28633-493">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="28633-493">The following example sets two environment variables.</span></span> <span data-ttu-id="28633-494">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak `Development`yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="28633-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="28633-495">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="28633-496">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="28633-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="28633-497">Ortamı doğrudan *Web. config* içinde ayarlamaya alternatif olarak, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil etmek de vardır.</span><span class="sxs-lookup"><span data-stu-id="28633-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="28633-498">Bu yaklaşım, proje yayımlandığında *Web. config* içinde ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="28633-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="28633-499">`ASPNETCORE_ENVIRONMENT` Ortam değişkenini yalnızca, Internet gibi `Development` güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28633-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="28633-500">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="28633-500">app_offline.htm</span></span>

<span data-ttu-id="28633-501">Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="28633-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="28633-502">Uygulama, içinde `shutdownTimeLimit`tanımlanan saniye sayısından sonra hala çalışıyorsa, ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="28633-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="28633-503">*App_offline. htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline. htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="28633-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="28633-504">*App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="28633-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="28633-505">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="28633-506">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="28633-507">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="28633-507">Start-up error page</span></span>

<span data-ttu-id="28633-508">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="28633-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="28633-509">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="28633-510">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="28633-511">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="28633-512">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="28633-513">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<hataları HttpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="28633-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="28633-514">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="28633-514">Log creation and redirection</span></span>

<span data-ttu-id="28633-515">ASP.NET Core modülü, `stdoutLogEnabled` `aspNetCore` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden `stdoutLogFile` yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="28633-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="28633-516">`stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28633-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="28633-517">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için kullanın `IIS AppPool\<app_pool_name>` ).</span><span class="sxs-lookup"><span data-stu-id="28633-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="28633-518">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="28633-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="28633-519">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="28633-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="28633-520">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="28633-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="28633-521">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="28633-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="28633-522">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="28633-523">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="28633-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="28633-524">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="28633-525">Günlük dosyası adı, alt çizgi ile ayrılmış `stdoutLogFile` yolun (genellikle *stdout*) son kesimine zaman damgası, işlem kimliği ve dosya uzantısı (*. log*) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="28633-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="28633-526">`stdoutLogFile` Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="28633-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="28633-527">Yanlış `stdoutLogEnabled` ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="28633-528">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="28633-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="28633-529">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki `.\log\`stdout günlüğünü yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="28633-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="28633-530">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="28633-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="28633-531">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak `\\?\%home%\LogFiles\stdout`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="28633-532">Ortam `%home` değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="28633-533">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="28633-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="28633-534">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="28633-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="28633-535">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="28633-536">`<handlerSettings>` Öğesini *Web. config*dosyasındaki `<aspNetCore>` öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="28633-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="28633-537">`<handlerSetting>` Değer için belirtilen yoldaki klasörler (önceki örnekteki*Günlükler* ) modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="28633-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="28633-538">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için kullanın `IIS AppPool\<app_pool_name>` ).</span><span class="sxs-lookup"><span data-stu-id="28633-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="28633-539">Hata ayıklama düzeyi`debugLevel`() değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="28633-540">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="28633-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="28633-541">HATA</span><span class="sxs-lookup"><span data-stu-id="28633-541">ERROR</span></span>
* <span data-ttu-id="28633-542">UYARI</span><span class="sxs-lookup"><span data-stu-id="28633-542">WARNING</span></span>
* <span data-ttu-id="28633-543">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="28633-543">INFO</span></span>
* <span data-ttu-id="28633-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="28633-544">TRACE</span></span>

<span data-ttu-id="28633-545">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="28633-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="28633-546">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="28633-546">CONSOLE</span></span>
* <span data-ttu-id="28633-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="28633-547">EVENTLOG</span></span>
* <span data-ttu-id="28633-548">DOSYA</span><span class="sxs-lookup"><span data-stu-id="28633-548">FILE</span></span>

<span data-ttu-id="28633-549">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="28633-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="28633-550">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="28633-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="28633-551">(Varsayılan: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="28633-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="28633-552">`ASPNETCORE_MODULE_DEBUG`&ndash; Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="28633-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="28633-553">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="28633-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="28633-554">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="28633-554">The size of the log isn't limited.</span></span> <span data-ttu-id="28633-555">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="28633-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="28633-556">*Web. config* dosyasındaki `aspNetCore` öğesinin bir örneği için bkz. [Web. config ile yapılandırma](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="28633-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="28633-557">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="28633-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="28633-558">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="28633-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="28633-559">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="28633-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="28633-560">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="28633-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="28633-561">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="28633-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="28633-562">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine (`ASPNETCORE_TOKEN`) ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="28633-563">Eşleştirme belirteci, her proxy istek için de bir üst`MS-ASPNETCORE-TOKEN`bilgi () olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="28633-564">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="28633-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="28633-565">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="28633-566">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="28633-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="28633-567">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="28633-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="28633-568">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="28633-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="28633-569">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="28633-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="28633-570">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="28633-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="28633-571">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde `OPT_NO_SHARED_CONFIG_CHECK` `1`ayarlanan parametre Ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="28633-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="28633-572">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="28633-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="28633-573">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="28633-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="28633-574">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="28633-574">Run the installer.</span></span>
1. <span data-ttu-id="28633-575">Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="28633-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="28633-576">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="28633-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="28633-577">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="28633-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="28633-578">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="28633-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="28633-579">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="28633-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="28633-580">*Aspnetcore. dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="28633-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="28633-581">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="28633-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="28633-582">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="28633-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="28633-583">Modülün barındırma paketi yükleyici günlükleri *C:\\Users\\% username%\\AppData\\local\\Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__\<zaman damgası>_000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="28633-584">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="28633-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="28633-585">Modül</span><span class="sxs-lookup"><span data-stu-id="28633-585">Module</span></span>

<span data-ttu-id="28633-586">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="28633-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="28633-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-589">%ProgramFiles%\IIS\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="28633-590">% ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="28633-591">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="28633-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="28633-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-593">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="28633-595">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="28633-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="28633-596">Şema</span><span class="sxs-lookup"><span data-stu-id="28633-596">Schema</span></span>

<span data-ttu-id="28633-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="28633-597">**IIS**</span></span>

* <span data-ttu-id="28633-598">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="28633-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="28633-599">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="28633-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="28633-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="28633-600">**IIS Express**</span></span>

* <span data-ttu-id="28633-601">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="28633-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="28633-602">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="28633-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="28633-603">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28633-603">Configuration</span></span>

<span data-ttu-id="28633-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="28633-604">**IIS**</span></span>

* <span data-ttu-id="28633-605">%Windir%\System32\inetsrv\config\applicationHost,config</span><span class="sxs-lookup"><span data-stu-id="28633-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="28633-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="28633-606">**IIS Express**</span></span>

* <span data-ttu-id="28633-607">Visual Studio: {APPLICATION ROOT}\\. Vs\config\applicationhost,config</span><span class="sxs-lookup"><span data-stu-id="28633-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="28633-608">*ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="28633-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="28633-609">Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="28633-610">ASP.NET Core modülü, Web isteklerini arka uca ASP.NET Core uygulamalarına iletmek için IIS ardışık düzenine takılan yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="28633-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="28633-611">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="28633-611">Supported Windows versions:</span></span>

* <span data-ttu-id="28633-612">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="28633-612">Windows 7 or later</span></span>
* <span data-ttu-id="28633-613">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="28633-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="28633-614">Modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-614">The module only works with Kestrel.</span></span> <span data-ttu-id="28633-615">Modül, [http. sys](xref:fundamentals/servers/httpsys)ile uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="28633-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="28633-616">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini de işler.</span><span class="sxs-lookup"><span data-stu-id="28633-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="28633-617">Modül, ilk istek ulaştığında ASP.NET Core App işlemini başlatır ve kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="28633-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="28633-618">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS 'de işlem içinde çalışan ASP.NET 4. x uygulamaları ile görüldüğü aynı davranıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="28633-619">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="28633-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="28633-621">İstekler Web 'den çekirdek modu HTTP. sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="28633-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="28633-622">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="28633-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="28633-623">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="28633-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="28633-624">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="28633-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="28633-625">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="28633-626">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="28633-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="28633-627">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="28633-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="28633-628">Ara yazılım ardışık düzeni isteği işler ve uygulamanın mantığına bir `HttpContext` örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="28633-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="28633-629">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="28633-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="28633-630">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="28633-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="28633-631">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="28633-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="28633-632">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules>..</span><span class="sxs-lookup"><span data-stu-id="28633-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="28633-633">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="28633-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="28633-634">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28633-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="28633-635">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="28633-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="28633-636">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="28633-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="28633-637">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="28633-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="28633-638">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="28633-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="28633-639">Web. config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28633-639">Configuration with web.config</span></span>

<span data-ttu-id="28633-640">ASP.NET Core modülü, sitenin *Web. config* dosyasındaki `aspNetCore` `system.webServer` düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="28633-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="28633-641">Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="28633-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="28633-642">Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="28633-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="28633-643">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak `\\?\%home%\LogFiles\stdout`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="28633-644">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="28633-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="28633-645">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications>..</span><span class="sxs-lookup"><span data-stu-id="28633-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="28633-646">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="28633-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="28633-647">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="28633-647">Attribute</span></span> | <span data-ttu-id="28633-648">Açıklama</span><span class="sxs-lookup"><span data-stu-id="28633-648">Description</span></span> | <span data-ttu-id="28633-649">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="28633-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="28633-650">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-650">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-651">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="28633-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="28633-652">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-653">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="28633-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="28633-654">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-655">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="28633-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="28633-656">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="28633-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="28633-657">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-658">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="28633-659">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="28633-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="28633-660">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="28633-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="28633-661">Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="28633-661">Default: `1`</span></span><br><span data-ttu-id="28633-662">Min`1`</span><span class="sxs-lookup"><span data-stu-id="28633-662">Min: `1`</span></span><br><span data-ttu-id="28633-663">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="28633-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="28633-664">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-664">Required string attribute.</span></span></p><p><span data-ttu-id="28633-665">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="28633-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="28633-666">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-666">Relative paths are supported.</span></span> <span data-ttu-id="28633-667">Yol ile `.`başlıyorsa, yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="28633-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="28633-668">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-669">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="28633-670">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="28633-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="28633-671">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="28633-671">Default: `10`</span></span><br><span data-ttu-id="28633-672">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-672">Min: `0`</span></span><br><span data-ttu-id="28633-673">Biçimlendir`100`</span><span class="sxs-lookup"><span data-stu-id="28633-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="28633-674">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="28633-675">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="28633-676">ASP.NET Core 2,1 veya üzeri `requestTimeout` sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="28633-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="28633-677">Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="28633-677">Default: `00:02:00`</span></span><br><span data-ttu-id="28633-678">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="28633-678">Min: `00:00:00`</span></span><br><span data-ttu-id="28633-679">Biçimlendir`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="28633-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="28633-680">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-681">*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="28633-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="28633-682">Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="28633-682">Default: `10`</span></span><br><span data-ttu-id="28633-683">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-683">Min: `0`</span></span><br><span data-ttu-id="28633-684">Biçimlendir`600`</span><span class="sxs-lookup"><span data-stu-id="28633-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="28633-685">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="28633-686">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="28633-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="28633-687">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="28633-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="28633-688">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="28633-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="28633-689">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="28633-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="28633-690">Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="28633-690">Default: `120`</span></span><br><span data-ttu-id="28633-691">Min`0`</span><span class="sxs-lookup"><span data-stu-id="28633-691">Min: `0`</span></span><br><span data-ttu-id="28633-692">Biçimlendir`3600`</span><span class="sxs-lookup"><span data-stu-id="28633-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="28633-693">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="28633-694">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="28633-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="28633-695">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="28633-695">Optional string attribute.</span></span></p><p><span data-ttu-id="28633-696">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="28633-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="28633-697">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="28633-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="28633-698">İle `.` başlayan tüm yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="28633-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="28633-699">Modülün günlük dosyasını oluşturması için yolda sunulan klasörlerin bulunması gerekir.</span><span class="sxs-lookup"><span data-stu-id="28633-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="28633-700">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="28633-701">Değer `.\logs\stdout` olarak sağlandıysa, bir 2/5/2018 Işlem 1934 kimliği ile 19:41:32 ' de ' *de kaydedildiğinde günlük* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="28633-702">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="28633-702">Setting environment variables</span></span>

<span data-ttu-id="28633-703">`processPath` Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="28633-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="28633-704">Bir `<environmentVariable>` `<environmentVariables>` koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin.</span><span class="sxs-lookup"><span data-stu-id="28633-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="28633-705">Bu bölümde ayarlanan ortam değişkenleri, aynı ada sahip sistem ortam değişkenleri ile çakışıyor.</span><span class="sxs-lookup"><span data-stu-id="28633-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="28633-706">Bir ortam değişkeni hem *Web. config* dosyasında hem de Windows 'un sistem düzeyinde ayarlandıysa, *Web. config* dosyasındaki değer, uygulamanın başlamasını önleyen sistem ortam değişkeni değerine (örneğin, `ASPNETCORE_ENVIRONMENT: Development;Development`) eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="28633-707">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="28633-707">The following example sets two environment variables.</span></span> <span data-ttu-id="28633-708">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak `Development`yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="28633-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="28633-709">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="28633-710">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="28633-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="28633-711">`ASPNETCORE_ENVIRONMENT` Ortam değişkenini yalnızca, Internet gibi `Development` güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28633-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="28633-712">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="28633-712">app_offline.htm</span></span>

<span data-ttu-id="28633-713">Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="28633-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="28633-714">Uygulama, içinde `shutdownTimeLimit`tanımlanan saniye sayısından sonra hala çalışıyorsa, ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="28633-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="28633-715">*App_offline. htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline. htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="28633-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="28633-716">*App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="28633-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="28633-717">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="28633-717">Start-up error page</span></span>

<span data-ttu-id="28633-718">ASP.NET Core modülü arka uç işlemini başlatamaz veya arka uç işlemi başlatılır, ancak yapılandırılmış bağlantı noktasında dinleme başarısız olursa, *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28633-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="28633-719">Bu sayfayı bastırın ve varsayılan IIS 502 durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="28633-720">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<hataları HttpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="28633-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502,5 işlem hatası durum kodu sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="28633-722">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="28633-722">Log creation and redirection</span></span>

<span data-ttu-id="28633-723">ASP.NET Core modülü, `stdoutLogEnabled` `aspNetCore` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden `stdoutLogFile` yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="28633-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="28633-724">`stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28633-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="28633-725">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için kullanın `IIS AppPool\<app_pool_name>` ).</span><span class="sxs-lookup"><span data-stu-id="28633-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="28633-726">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="28633-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="28633-727">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="28633-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="28633-728">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="28633-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="28633-729">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="28633-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="28633-730">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="28633-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="28633-731">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="28633-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="28633-732">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="28633-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="28633-733">Günlük dosyası adı, alt çizgi ile ayrılmış `stdoutLogFile` yolun (genellikle *stdout*) son kesimine zaman damgası, işlem kimliği ve dosya uzantısı (*. log*) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="28633-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="28633-734">`stdoutLogFile` Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="28633-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="28633-735">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki `.\log\`stdout günlüğünü yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="28633-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="28633-736">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="28633-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="28633-737">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak `\\?\%home%\LogFiles\stdout`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="28633-738">Ortam `%home` değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="28633-739">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="28633-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="28633-740">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="28633-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="28633-741">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="28633-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="28633-742">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="28633-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="28633-743">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="28633-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="28633-744">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="28633-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="28633-745">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine (`ASPNETCORE_TOKEN`) ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="28633-746">Eşleştirme belirteci, her proxy istek için de bir üst`MS-ASPNETCORE-TOKEN`bilgi () olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="28633-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="28633-747">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="28633-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="28633-748">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="28633-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="28633-749">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="28633-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="28633-750">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="28633-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="28633-751">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="28633-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="28633-752">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="28633-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="28633-753">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="28633-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="28633-754">IIS paylaşılan yapılandırması kullanırken, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="28633-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="28633-755">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="28633-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="28633-756">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="28633-756">Run the installer.</span></span>
1. <span data-ttu-id="28633-757">Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="28633-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="28633-758">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="28633-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="28633-759">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="28633-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="28633-760">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="28633-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="28633-761">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="28633-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="28633-762">*Aspnetcore. dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="28633-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="28633-763">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="28633-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="28633-764">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="28633-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="28633-765">Modülün barındırma paketi yükleyici günlükleri *C:\\Users\\% username%\\AppData\\local\\Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__\<zaman damgası>_000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="28633-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="28633-766">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="28633-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="28633-767">Modül</span><span class="sxs-lookup"><span data-stu-id="28633-767">Module</span></span>

<span data-ttu-id="28633-768">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="28633-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="28633-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="28633-771">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="28633-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="28633-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="28633-773">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="28633-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="28633-774">Şema</span><span class="sxs-lookup"><span data-stu-id="28633-774">Schema</span></span>

<span data-ttu-id="28633-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="28633-775">**IIS**</span></span>

* <span data-ttu-id="28633-776">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="28633-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="28633-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="28633-777">**IIS Express**</span></span>

* <span data-ttu-id="28633-778">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="28633-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="28633-779">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28633-779">Configuration</span></span>

<span data-ttu-id="28633-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="28633-780">**IIS**</span></span>

* <span data-ttu-id="28633-781">%Windir%\System32\inetsrv\config\applicationHost,config</span><span class="sxs-lookup"><span data-stu-id="28633-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="28633-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="28633-782">**IIS Express**</span></span>

* <span data-ttu-id="28633-783">Visual Studio: {APPLICATION ROOT}\\. Vs\config\applicationhost,config</span><span class="sxs-lookup"><span data-stu-id="28633-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="28633-784">*ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="28633-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="28633-785">Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="28633-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="28633-786">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="28633-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="28633-787">[ASP.NET Core modülü başvuru kaynağı (ana dal)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; belirli bir sürümü seçmek için **dal** açılan listesini kullanın (örneğin, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="28633-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
