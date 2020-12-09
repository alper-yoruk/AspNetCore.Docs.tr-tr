---
title: ASP.NET Core Modülü
author: rick-anderson
description: IIS ile ASP.NET Core uygulamalarını barındırmak için ASP.NET Core modülü hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d0e6c0c31890c58aaca936fc6f1e92cb9a1ab456
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901242"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="bff5c-103">ASP.NET Core Modülü</span><span class="sxs-lookup"><span data-stu-id="bff5c-103">ASP.NET Core Module</span></span>

<span data-ttu-id="bff5c-104">, [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris](https://github.com/Tratcher)No, [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)ve ka [kotalık](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="bff5c-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bff5c-105">ASP.NET Core modülü, IIS işlem hattına takılan ve ASP.NET Core uygulamaların IIS ile çalışmasına izin veren yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="bff5c-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="bff5c-106">ASP.NET Core uygulamalarını IIS ile birlikte çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="bff5c-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="bff5c-107">`w3wp.exe` [İşlem içi barındırma modeli](xref:host-and-deploy/iis/in-process-hosting)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırma.</span><span class="sxs-lookup"><span data-stu-id="bff5c-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="bff5c-108">Web isteklerini, [işlem dışı barındırma modeli](xref:host-and-deploy/iis/out-of-process-hosting)olarak adlandırılan Kestrel sunucusunu çalıştıran bir arka uç ASP.NET Core uygulamasına iletme.</span><span class="sxs-lookup"><span data-stu-id="bff5c-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="bff5c-109">Barındırma modellerinin her biri arasında denge vardır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="bff5c-110">Varsayılan olarak, daha iyi performans ve tanılama nedeniyle işlem içi barındırma modeli kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="bff5c-111">ASP.NET Core modülünü Install</span><span class="sxs-lookup"><span data-stu-id="bff5c-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="bff5c-112">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="bff5c-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="bff5c-113">Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="bff5c-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="bff5c-114">ASP.NET Core modülünü yükleme veya modülün farklı sürümlerini yükleme hakkında daha fazla bilgi için bkz. [.NET Core barındırma paketi yükleme](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="bff5c-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="bff5c-115">ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="bff5c-116">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="bff5c-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="bff5c-117">`w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="bff5c-118">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="bff5c-119">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="bff5c-119">Supported Windows versions:</span></span>

* <span data-ttu-id="bff5c-120">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="bff5c-120">Windows 7 or later</span></span>
* <span data-ttu-id="bff5c-121">Windows Server 2012 R2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="bff5c-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="bff5c-122">İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="bff5c-123">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="bff5c-124">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="bff5c-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="bff5c-125">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="bff5c-126">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="bff5c-126">In-process hosting model</span></span>

<span data-ttu-id="bff5c-127">Uygulamalar, işlem içi barındırma modelinde varsayılan olarak ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bff5c-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="bff5c-128">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="bff5c-129">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="bff5c-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="bff5c-130">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="bff5c-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="bff5c-131">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="bff5c-132">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="bff5c-133">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="bff5c-134">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="bff5c-135">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="bff5c-136">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-136">Use one app pool per app.</span></span>

* <span data-ttu-id="bff5c-137">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtıma el ile bir [ `app_offline.htm` Dosya](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="bff5c-138">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="bff5c-139">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="bff5c-140">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="bff5c-140">Client disconnects are detected.</span></span> <span data-ttu-id="bff5c-141">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*)İstemci bağlantısı kesildiğinde iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="bff5c-142">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, `C:\Windows\System32\inetsrv` için `w3wp.exe` ).</span><span class="sxs-lookup"><span data-stu-id="bff5c-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="bff5c-143">Uygulamanın geçerli dizinini ayarlayan örnek kod için, [ `CurrentDirectoryHelpers` sınıfına](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="bff5c-144">Yöntemini çağırın `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="bff5c-145"><xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="bff5c-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="bff5c-146">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="bff5c-147">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="bff5c-148">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="bff5c-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="bff5c-149">[Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="bff5c-150">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="bff5c-150">Out-of-process hosting model</span></span>

<span data-ttu-id="bff5c-151">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini `OutOfProcess` Proje dosyasında () olarak ayarlayın `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="bff5c-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="bff5c-152">İşlem içi barındırma `InProcess` , varsayılan değer olan ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="bff5c-153">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="bff5c-154">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="bff5c-155">İşlem dışı için şunu [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="bff5c-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="bff5c-156">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="bff5c-157">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="bff5c-158">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-158">Hosting model changes</span></span>

<span data-ttu-id="bff5c-159">`hostingModel`Ayar `web.config` dosyada değiştirilirse ( [ile `web.config` yapılandırma](#configuration-with-webconfig) bölümünde açıklanmıştır), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="bff5c-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="bff5c-160">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="bff5c-161">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="bff5c-162">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="bff5c-162">Process name</span></span>

<span data-ttu-id="bff5c-163">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="bff5c-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="bff5c-164">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="bff5c-165">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="bff5c-166">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="bff5c-167">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="bff5c-168">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="bff5c-169">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="bff5c-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="bff5c-170">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="bff5c-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="bff5c-171">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="bff5c-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="bff5c-172">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bff5c-172">Configuration with web.config</span></span>

<span data-ttu-id="bff5c-173">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="bff5c-174">Aşağıdaki `web.config` Dosya, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="bff5c-175">Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="bff5c-176"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="bff5c-177">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="bff5c-178">Yol, stdout günlüklerini `LogFiles` , hizmet tarafından otomatik olarak oluşturulan bir konum olan klasöre kaydeder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="bff5c-179">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="bff5c-180">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="bff5c-181">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="bff5c-181">Attribute</span></span> | <span data-ttu-id="bff5c-182">Açıklama</span><span class="sxs-lookup"><span data-stu-id="bff5c-182">Description</span></span> | <span data-ttu-id="bff5c-183">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="bff5c-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="bff5c-184">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-184">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-185">**ProcessPath** içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="bff5c-186">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-187">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="bff5c-188">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-189">True ise, belirteç `%ASPNETCORE_PORT%` istek başına üst bilgi olarak dinleyen alt işleme iletilir `'MS-ASPNETCORE-WINAUTHTOKEN'` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="bff5c-190">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="bff5c-191">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-191">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-192">Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="bff5c-193">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-194">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="bff5c-195">&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="bff5c-196">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="bff5c-197">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="bff5c-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="bff5c-198">Varsayılanını `1`</span><span class="sxs-lookup"><span data-stu-id="bff5c-198">Default: `1`</span></span><br><span data-ttu-id="bff5c-199">Min `1`</span><span class="sxs-lookup"><span data-stu-id="bff5c-199">Min: `1`</span></span><br><span data-ttu-id="bff5c-200">Biçimlendir `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="bff5c-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="bff5c-201">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-201">Required string attribute.</span></span></p><p><span data-ttu-id="bff5c-202">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="bff5c-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="bff5c-203">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-203">Relative paths are supported.</span></span> <span data-ttu-id="bff5c-204">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="bff5c-205">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-206">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="bff5c-207">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="bff5c-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="bff5c-208">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="bff5c-209">Varsayılanını `10`</span><span class="sxs-lookup"><span data-stu-id="bff5c-209">Default: `10`</span></span><br><span data-ttu-id="bff5c-210">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-210">Min: `0`</span></span><br><span data-ttu-id="bff5c-211">Biçimlendir `100`</span><span class="sxs-lookup"><span data-stu-id="bff5c-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="bff5c-212">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="bff5c-213">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="bff5c-214">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="bff5c-215">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="bff5c-216">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="bff5c-217">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="bff5c-218">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına* neden olur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="bff5c-219">Varsayılanını `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-219">Default: `00:02:00`</span></span><br><span data-ttu-id="bff5c-220">Min `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-220">Min: `00:00:00`</span></span><br><span data-ttu-id="bff5c-221">Biçimlendir `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="bff5c-222">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-223">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="bff5c-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="bff5c-224">Varsayılanını `10`</span><span class="sxs-lookup"><span data-stu-id="bff5c-224">Default: `10`</span></span><br><span data-ttu-id="bff5c-225">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-225">Min: `0`</span></span><br><span data-ttu-id="bff5c-226">Biçimlendir `600`</span><span class="sxs-lookup"><span data-stu-id="bff5c-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="bff5c-227">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-228">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="bff5c-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="bff5c-229">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="bff5c-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="bff5c-230">*İşlem içi* barındırma sırasında: **işlem yeniden başlatılmaz** ve **rapidFailsPerMinute** ayarını **kullanmaz.**</span><span class="sxs-lookup"><span data-stu-id="bff5c-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="bff5c-231">*İşlem dışı* barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** sayısını başlatamadıkça sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="bff5c-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="bff5c-232">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="bff5c-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="bff5c-233">Varsayılanını `120`</span><span class="sxs-lookup"><span data-stu-id="bff5c-233">Default: `120`</span></span><br><span data-ttu-id="bff5c-234">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-234">Min: `0`</span></span><br><span data-ttu-id="bff5c-235">Biçimlendir `3600`</span><span class="sxs-lookup"><span data-stu-id="bff5c-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="bff5c-236">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-237">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile** içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="bff5c-238">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-238">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-239">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="bff5c-240">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="bff5c-241">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="bff5c-242">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="bff5c-243">Alt çizgi sınırlayıcılarını kullanma, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ), **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="bff5c-244">`.\logs\stdout`Değer olarak sağlandıysa, `stdout_20180205194132_1934.log` `logs` 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de kaydedildiğinde, bir örnek stdout günlüğü klasöre kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="bff5c-245">Ortam değişkenlerini belirleme</span><span class="sxs-lookup"><span data-stu-id="bff5c-245">Set environment variables</span></span>

<span data-ttu-id="bff5c-246">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="bff5c-247">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="bff5c-248">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="bff5c-249">Aşağıdaki örnek ' de iki ortam değişkenini ayarlar `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="bff5c-250">`ASPNETCORE_ENVIRONMENT` uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="bff5c-251">Bir geliştirici `web.config` , uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için dosyada bu değeri geçici olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="bff5c-252">`CONFIG_DIR` , geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="bff5c-253">Ortamın doğrudan ' de ayarlanmasına alternatif olarak `web.config` , `<EnvironmentName>` [Yayımlama profili ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına özelliği dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="bff5c-254">Bu yaklaşım, proje yayımlandığında içindeki ortamı ayarlar `web.config` :</span><span class="sxs-lookup"><span data-stu-id="bff5c-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="bff5c-255">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="bff5c-256">Uygulamanın kök dizininde ada sahip bir dosya `app_offline.htm` algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya çalışır ve gelen istekleri işlemeyi durdurur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="bff5c-257">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="bff5c-258">`app_offline.htm`Dosya mevcut olsa da, ASP.NET Core modülü dosya içeriğini geri göndererek isteklere yanıt verir `app_offline.htm` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="bff5c-259">`app_offline.htm`Dosya kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="bff5c-260">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="bff5c-261">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="bff5c-262">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="bff5c-262">Start-up error page</span></span>

<span data-ttu-id="bff5c-263">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="bff5c-264">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="bff5c-265">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="bff5c-266">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="bff5c-267">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="bff5c-268">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http `<httpErrors>` hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="bff5c-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="bff5c-269">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="bff5c-269">Log creation and redirection</span></span>

<span data-ttu-id="bff5c-270">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="bff5c-271">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="bff5c-272">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="bff5c-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="bff5c-273">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="bff5c-274">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="bff5c-275">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="bff5c-276">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="bff5c-277">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bff5c-278">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="bff5c-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="bff5c-279">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="bff5c-280">Günlük dosyası adı, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ), yolun son kesimine `stdoutLogFile` (genellikle `stdout` ) alt çizgi ile ayrılmış olarak eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="bff5c-281">`stdoutLogFile`Yol ile sonlanıyorsa `stdout` , 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı dosya adına sahiptir `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="bff5c-282">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="bff5c-283">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="bff5c-284">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="bff5c-285">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="bff5c-286">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="bff5c-287">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="bff5c-288">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="bff5c-289">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="bff5c-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="bff5c-290">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="bff5c-291">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="bff5c-292">`<handlerSettings>`Öğesini `<aspNetCore>` içindeki öğesine ekleyin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="bff5c-293">İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="bff5c-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="bff5c-294">Yoldaki tüm klasörler ( `logs` Önceki örnekte), günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="bff5c-295">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (Bu `IIS AppPool\{APP POOL NAME}` , yer tutucunun, `{APP POOL NAME}` yazma izni sağlamak için uygulama havuzu adı olduğu yerdir).</span><span class="sxs-lookup"><span data-stu-id="bff5c-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="bff5c-296">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="bff5c-297">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="bff5c-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="bff5c-298">HATA</span><span class="sxs-lookup"><span data-stu-id="bff5c-298">ERROR</span></span>
* <span data-ttu-id="bff5c-299">UYARI</span><span class="sxs-lookup"><span data-stu-id="bff5c-299">WARNING</span></span>
* <span data-ttu-id="bff5c-300">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="bff5c-300">INFO</span></span>
* <span data-ttu-id="bff5c-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="bff5c-301">TRACE</span></span>

<span data-ttu-id="bff5c-302">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="bff5c-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="bff5c-303">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="bff5c-303">CONSOLE</span></span>
* <span data-ttu-id="bff5c-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="bff5c-304">EVENTLOG</span></span>
* <span data-ttu-id="bff5c-305">DOSYA</span><span class="sxs-lookup"><span data-stu-id="bff5c-305">FILE</span></span>

<span data-ttu-id="bff5c-306">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="bff5c-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="bff5c-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="bff5c-308">(Varsayılan: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="bff5c-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="bff5c-309">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="bff5c-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="bff5c-310">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="bff5c-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="bff5c-311">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="bff5c-311">The size of the log isn't limited.</span></span> <span data-ttu-id="bff5c-312">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="bff5c-313">Dosyadaki öğenin bir örneği için [web.configyapılandırma ](#configuration-with-webconfig) konusuna bakın `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="bff5c-314">Yığın boyutunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="bff5c-314">Modify the stack size</span></span>

<span data-ttu-id="bff5c-315">*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="bff5c-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="bff5c-316">Yönetilen yığın boyutunu `stackSize` içindeki bayt cinsinden ayarını kullanarak yapılandırın `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="bff5c-317">Varsayılan boyut 1.048.576 bayttır (1 MB).</span><span class="sxs-lookup"><span data-stu-id="bff5c-317">The default size is 1,048,576 bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="bff5c-318">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="bff5c-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="bff5c-319">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="bff5c-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="bff5c-320">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="bff5c-321">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="bff5c-322">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="bff5c-323">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="bff5c-324">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="bff5c-325">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="bff5c-326">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="bff5c-327">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="bff5c-328">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="bff5c-329">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="bff5c-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="bff5c-330">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="bff5c-331">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki dosyadaki modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="bff5c-332">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="bff5c-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="bff5c-333">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="bff5c-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="bff5c-334">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="bff5c-335">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-335">Run the installer.</span></span>
1. <span data-ttu-id="bff5c-336">Güncelleştirilmiş `applicationHost.config` dosyayı paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="bff5c-337">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="bff5c-338">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="bff5c-339">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="bff5c-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="bff5c-340">Barındırma sisteminde öğesine gidin `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="bff5c-341">Dosyayı bulun `aspnetcore.dll` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="bff5c-342">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="bff5c-343">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="bff5c-344">Modülün barındırma paketi yükleyici günlükleri konumunda bulunur `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="bff5c-345">Dosya adı `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="bff5c-346">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="bff5c-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="bff5c-347">Modül</span><span class="sxs-lookup"><span data-stu-id="bff5c-347">Module</span></span>

<span data-ttu-id="bff5c-348">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="bff5c-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="bff5c-349">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="bff5c-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="bff5c-350">Şema</span><span class="sxs-lookup"><span data-stu-id="bff5c-350">Schema</span></span>

<span data-ttu-id="bff5c-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="bff5c-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="bff5c-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="bff5c-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="bff5c-353">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bff5c-353">Configuration</span></span>

<span data-ttu-id="bff5c-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="bff5c-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="bff5c-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="bff5c-355">**IIS Express**</span></span>

* <span data-ttu-id="bff5c-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="bff5c-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="bff5c-357">*iisexpress.exe* CLı `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="bff5c-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="bff5c-358">Dosyalar dosyada arayarak bulunabilir `aspnetcore` `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bff5c-359">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="bff5c-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="bff5c-360">`w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="bff5c-361">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="bff5c-362">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="bff5c-362">Supported Windows versions:</span></span>

* <span data-ttu-id="bff5c-363">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="bff5c-363">Windows 7 or later</span></span>
* <span data-ttu-id="bff5c-364">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="bff5c-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="bff5c-365">İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="bff5c-366">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="bff5c-367">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="bff5c-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="bff5c-368">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="bff5c-369">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="bff5c-369">In-process hosting model</span></span>

<span data-ttu-id="bff5c-370">Uygulamayı işlem içi barındırma için yapılandırmak için, `<AspNetCoreHostingModel>` özelliği uygulamanın proje dosyasına `InProcess` (işlem dışı barındırma ile ayarlanır `OutOfProcess` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bff5c-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="bff5c-371">İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="bff5c-372">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="bff5c-373">`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="bff5c-374">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="bff5c-375">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="bff5c-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="bff5c-376">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="bff5c-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="bff5c-377">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="bff5c-378">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="bff5c-379">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="bff5c-380">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="bff5c-381">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="bff5c-382">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-382">Use one app pool per app.</span></span>

* <span data-ttu-id="bff5c-383">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtıma el ile bir [app_offline.htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="bff5c-384">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="bff5c-385">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="bff5c-386">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="bff5c-386">Client disconnects are detected.</span></span> <span data-ttu-id="bff5c-387">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="bff5c-388">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *w3wp.exe* için *c:\Windows\System32\inetsrv* ).</span><span class="sxs-lookup"><span data-stu-id="bff5c-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="bff5c-389">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="bff5c-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="bff5c-390">Yöntemini çağırın `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="bff5c-391"><xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="bff5c-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="bff5c-392">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="bff5c-393">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="bff5c-394">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="bff5c-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="bff5c-395">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="bff5c-395">Out-of-process hosting model</span></span>

<span data-ttu-id="bff5c-396">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, proje dosyasında aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="bff5c-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="bff5c-397">`<AspNetCoreHostingModel>`Özelliği belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="bff5c-398">`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="bff5c-399">`<AspNetCoreHostingModel>`Özelliğin değerini olarak ayarlayın `OutOfProcess` (işlem içi barındırma ile ayarlanır `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="bff5c-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="bff5c-400">Değer büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="bff5c-401">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="bff5c-402">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="bff5c-403">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="bff5c-404">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="bff5c-405">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-405">Hosting model changes</span></span>

<span data-ttu-id="bff5c-406">Ayar, `hostingModel` *web.config* dosyasında değiştirilirse ( [web.configile yapılandırma](#configuration-with-webconfig) bölümünde açıklanan), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="bff5c-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="bff5c-407">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="bff5c-408">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="bff5c-409">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="bff5c-409">Process name</span></span>

<span data-ttu-id="bff5c-410">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="bff5c-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="bff5c-411">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="bff5c-412">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="bff5c-413">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="bff5c-414">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="bff5c-415">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="bff5c-416">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="bff5c-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="bff5c-417">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="bff5c-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="bff5c-418">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="bff5c-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="bff5c-419">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bff5c-419">Configuration with web.config</span></span>

<span data-ttu-id="bff5c-420">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="bff5c-421">Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="bff5c-422">Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="bff5c-423"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="bff5c-424">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="bff5c-425">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="bff5c-426">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="bff5c-427">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="bff5c-428">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="bff5c-428">Attribute</span></span> | <span data-ttu-id="bff5c-429">Açıklama</span><span class="sxs-lookup"><span data-stu-id="bff5c-429">Description</span></span> | <span data-ttu-id="bff5c-430">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="bff5c-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="bff5c-431">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-431">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-432">İçinde belirtilen yürütülebilirin bağımsız değişkenleri `processPath` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="bff5c-433">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-434">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="bff5c-435">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-436">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="bff5c-437">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="bff5c-438">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-438">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-439">Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="bff5c-440">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-441">Ayarda belirtilen işlemin örnek sayısını belirtir `processPath` . Bu ayar, uygulama başına bir işlem yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="bff5c-442">&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="bff5c-443">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="bff5c-444">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="bff5c-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="bff5c-445">Varsayılanını `1`</span><span class="sxs-lookup"><span data-stu-id="bff5c-445">Default: `1`</span></span><br><span data-ttu-id="bff5c-446">Min `1`</span><span class="sxs-lookup"><span data-stu-id="bff5c-446">Min: `1`</span></span><br><span data-ttu-id="bff5c-447">Biçimlendir `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="bff5c-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="bff5c-448">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-448">Required string attribute.</span></span></p><p><span data-ttu-id="bff5c-449">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="bff5c-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="bff5c-450">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-450">Relative paths are supported.</span></span> <span data-ttu-id="bff5c-451">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="bff5c-452">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-453">İçinde belirtilen işlemin `processPath` dakika başına kilitlenme için izin verileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="bff5c-454">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="bff5c-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="bff5c-455">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="bff5c-456">Varsayılanını `10`</span><span class="sxs-lookup"><span data-stu-id="bff5c-456">Default: `10`</span></span><br><span data-ttu-id="bff5c-457">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-457">Min: `0`</span></span><br><span data-ttu-id="bff5c-458">Biçimlendir `100`</span><span class="sxs-lookup"><span data-stu-id="bff5c-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="bff5c-459">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="bff5c-460">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="bff5c-461">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="bff5c-462">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="bff5c-463">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="bff5c-464">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="bff5c-465">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına* neden olur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="bff5c-466">Varsayılanını `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-466">Default: `00:02:00`</span></span><br><span data-ttu-id="bff5c-467">Min `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-467">Min: `00:00:00`</span></span><br><span data-ttu-id="bff5c-468">Biçimlendir `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="bff5c-469">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-470">Dosya algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre `app_offline.htm` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="bff5c-471">Varsayılanını `10`</span><span class="sxs-lookup"><span data-stu-id="bff5c-471">Default: `10`</span></span><br><span data-ttu-id="bff5c-472">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-472">Min: `0`</span></span><br><span data-ttu-id="bff5c-473">Biçimlendir `600`</span><span class="sxs-lookup"><span data-stu-id="bff5c-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="bff5c-474">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-475">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="bff5c-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="bff5c-476">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="bff5c-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="bff5c-477">*İşlem içi* barındırma sırasında: **işlem yeniden başlatılmaz** ve ayarı kullanılmaz **not** `rapidFailsPerMinute` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="bff5c-478">*İşlem dışı* barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın `rapidFailsPerMinute` son geçen dakikada bir kez başlamamaya devam etmediği sürece sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="bff5c-479">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="bff5c-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="bff5c-480">Varsayılanını `120`</span><span class="sxs-lookup"><span data-stu-id="bff5c-480">Default: `120`</span></span><br><span data-ttu-id="bff5c-481">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-481">Min: `0`</span></span><br><span data-ttu-id="bff5c-482">Biçimlendir `3600`</span><span class="sxs-lookup"><span data-stu-id="bff5c-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="bff5c-483">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-484">True ise, içinde belirtilen işlem için **stdout** ve **stderr** , `processPath` **stdoutLogFile** içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="bff5c-485">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-485">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-486">`stdout` `stderr` İçinde belirtilen işlemden ve bu işlemin günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="bff5c-487">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="bff5c-488">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="bff5c-489">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="bff5c-490">Alt çizgi sınırlayıcılarını kullanma, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ) yolun son kesimine eklenir `stdoutLogFile` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="bff5c-491">`.\logs\stdout`Değer olarak sağlandıysa, `stdout_20180205194132_1934.log` `logs` 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de kaydedildiğinde, bir örnek stdout günlüğü klasöre kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="bff5c-492">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="bff5c-492">Setting environment variables</span></span>

<span data-ttu-id="bff5c-493">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="bff5c-494">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="bff5c-495">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="bff5c-496">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bff5c-496">The following example sets two environment variables.</span></span> <span data-ttu-id="bff5c-497">`ASPNETCORE_ENVIRONMENT` uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="bff5c-498">Bir geliştirici `web.config` , uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için dosyada bu değeri geçici olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="bff5c-499">`CONFIG_DIR` , geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="bff5c-500">Ortamın doğrudan ' de ayarlanmasına bir alternatifi, `web.config` `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="bff5c-501">Bu yaklaşım, proje yayımlandığında içindeki ortamı ayarlar `web.config` :</span><span class="sxs-lookup"><span data-stu-id="bff5c-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="bff5c-502">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="bff5c-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="bff5c-503">app_offline.htm</span></span>

<span data-ttu-id="bff5c-504">Uygulamanın kök dizininde ada sahip bir dosya `app_offline.htm` algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya çalışır ve gelen istekleri işlemeyi durdurur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="bff5c-505">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="bff5c-506">`app_offline.htm`Dosya mevcut olsa da, ASP.NET Core modülü dosya içeriğini geri göndererek isteklere yanıt verir `app_offline.htm` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="bff5c-507">`app_offline.htm`Dosya kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="bff5c-508">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="bff5c-509">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="bff5c-510">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="bff5c-510">Start-up error page</span></span>

<span data-ttu-id="bff5c-511">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="bff5c-512">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="bff5c-513">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="bff5c-514">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="bff5c-515">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="bff5c-516">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="bff5c-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="bff5c-517">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="bff5c-517">Log creation and redirection</span></span>

<span data-ttu-id="bff5c-518">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="bff5c-519">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="bff5c-520">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\{APP POOL NAME}` yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu yazma iznini sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="bff5c-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="bff5c-521">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="bff5c-522">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="bff5c-523">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="bff5c-524">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="bff5c-525">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bff5c-526">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="bff5c-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="bff5c-527">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="bff5c-528">Günlük dosyası adı, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ), yolun son kesimine `stdoutLogFile` (genellikle `stdout` ) alt çizgi ile ayrılmış olarak eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="bff5c-529">`stdoutLogFile`Yol ile sonlanıyorsa `stdout` , 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı dosya adına sahiptir `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="bff5c-530">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="bff5c-531">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="bff5c-532">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="bff5c-533">Uygulama havuzu Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="bff5c-534">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="bff5c-535">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="bff5c-536">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="bff5c-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="bff5c-537">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="bff5c-538">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="bff5c-539">`<handlerSettings>`Öğesini `<aspNetCore>` içindeki öğesine ekleyin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="bff5c-540">İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="bff5c-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="bff5c-541">Değer için belirtilen yoldaki klasörler `<handlerSetting>` ( `logs` Önceki örnekte) modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="bff5c-542">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\{APP POOL NAME}` yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu yazma iznini sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="bff5c-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="bff5c-543">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="bff5c-544">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="bff5c-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="bff5c-545">HATA</span><span class="sxs-lookup"><span data-stu-id="bff5c-545">ERROR</span></span>
* <span data-ttu-id="bff5c-546">UYARI</span><span class="sxs-lookup"><span data-stu-id="bff5c-546">WARNING</span></span>
* <span data-ttu-id="bff5c-547">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="bff5c-547">INFO</span></span>
* <span data-ttu-id="bff5c-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="bff5c-548">TRACE</span></span>

<span data-ttu-id="bff5c-549">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="bff5c-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="bff5c-550">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="bff5c-550">CONSOLE</span></span>
* <span data-ttu-id="bff5c-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="bff5c-551">EVENTLOG</span></span>
* <span data-ttu-id="bff5c-552">DOSYA</span><span class="sxs-lookup"><span data-stu-id="bff5c-552">FILE</span></span>

<span data-ttu-id="bff5c-553">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="bff5c-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="bff5c-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="bff5c-555">(Varsayılan: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="bff5c-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="bff5c-556">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="bff5c-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="bff5c-557">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="bff5c-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="bff5c-558">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="bff5c-558">The size of the log isn't limited.</span></span> <span data-ttu-id="bff5c-559">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="bff5c-560">Dosyadaki öğenin bir örneği için [web.configyapılandırma ](#configuration-with-webconfig) konusuna bakın `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="bff5c-561">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="bff5c-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="bff5c-562">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="bff5c-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="bff5c-563">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="bff5c-564">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="bff5c-565">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="bff5c-566">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="bff5c-567">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="bff5c-568">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="bff5c-569">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="bff5c-570">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="bff5c-571">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="bff5c-572">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="bff5c-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="bff5c-573">ASP.NET Core modülü yükleyicisi hesap ayrıcalıklarıyla çalışır `TrustedInstaller` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="bff5c-574">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki dosyadaki modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="bff5c-575">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="bff5c-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="bff5c-576">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="bff5c-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="bff5c-577">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="bff5c-578">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-578">Run the installer.</span></span>
1. <span data-ttu-id="bff5c-579">Güncelleştirilmiş `applicationHost.config` dosyayı paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="bff5c-580">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="bff5c-581">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="bff5c-582">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="bff5c-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="bff5c-583">Barındırma sisteminde öğesine gidin `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="bff5c-584">Dosyayı bulun `aspnetcore.dll` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="bff5c-585">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="bff5c-586">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="bff5c-587">Modülün barındırma paketi yükleyici günlükleri konumunda bulunur `C:\\Users\\%UserName%\\AppData\\Local\\Temp` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="bff5c-588">Dosyanın adı `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` , yer tutucunun `{TIMESTAMP}` zaman damgası olduğu yerdir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="bff5c-589">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="bff5c-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="bff5c-590">Modül</span><span class="sxs-lookup"><span data-stu-id="bff5c-590">Module</span></span>

<span data-ttu-id="bff5c-591">**IIS (X86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="bff5c-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="bff5c-592">**IIS Express (X86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="bff5c-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="bff5c-593">Şema</span><span class="sxs-lookup"><span data-stu-id="bff5c-593">Schema</span></span>

<span data-ttu-id="bff5c-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="bff5c-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="bff5c-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="bff5c-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="bff5c-596">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bff5c-596">Configuration</span></span>

<span data-ttu-id="bff5c-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="bff5c-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="bff5c-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="bff5c-598">**IIS Express**</span></span>

* <span data-ttu-id="bff5c-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="bff5c-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="bff5c-600">*iisexpress.exe* CLı `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="bff5c-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="bff5c-601">Dosyalar dosyada arayarak bulunabilir `aspnetcore` `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="bff5c-602">ASP.NET Core modülü, Web isteklerini arka uca ASP.NET Core uygulamalarına iletmek için IIS ardışık düzenine takılan yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="bff5c-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="bff5c-603">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="bff5c-603">Supported Windows versions:</span></span>

* <span data-ttu-id="bff5c-604">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="bff5c-604">Windows 7 or later</span></span>
* <span data-ttu-id="bff5c-605">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="bff5c-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="bff5c-606">Modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-606">The module only works with Kestrel.</span></span> <span data-ttu-id="bff5c-607">Modül [HTTP.sys](xref:fundamentals/servers/httpsys)uyumsuz.</span><span class="sxs-lookup"><span data-stu-id="bff5c-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="bff5c-608">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini de işler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="bff5c-609">Modül, ilk istek ulaştığında ASP.NET Core App işlemini başlatır ve kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="bff5c-610">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS 'de işlem içinde çalışan ASP.NET 4. x uygulamaları ile görüldüğü aynı davranıştır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="bff5c-611">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core Modülü](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="bff5c-613">İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="bff5c-614">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="bff5c-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="bff5c-615">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="bff5c-616">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="bff5c-617">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="bff5c-618">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="bff5c-619">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="bff5c-620">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="bff5c-621">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="bff5c-622">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="bff5c-623">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="bff5c-624">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="bff5c-625">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="bff5c-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="bff5c-626">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="bff5c-627">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="bff5c-628">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="bff5c-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="bff5c-629">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="bff5c-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="bff5c-630">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="bff5c-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="bff5c-631">web.config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bff5c-631">Configuration with web.config</span></span>

<span data-ttu-id="bff5c-632">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="bff5c-633">Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="bff5c-634">Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="bff5c-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="bff5c-635">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="bff5c-636">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="bff5c-637">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="bff5c-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="bff5c-638">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="bff5c-639">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="bff5c-639">Attribute</span></span> | <span data-ttu-id="bff5c-640">Açıklama</span><span class="sxs-lookup"><span data-stu-id="bff5c-640">Description</span></span> | <span data-ttu-id="bff5c-641">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="bff5c-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="bff5c-642">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-642">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-643">**ProcessPath** içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="bff5c-644">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-645">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="bff5c-646">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-647">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="bff5c-648">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="bff5c-649">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-650">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="bff5c-651">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="bff5c-652">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="bff5c-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="bff5c-653">Varsayılanını `1`</span><span class="sxs-lookup"><span data-stu-id="bff5c-653">Default: `1`</span></span><br><span data-ttu-id="bff5c-654">Min `1`</span><span class="sxs-lookup"><span data-stu-id="bff5c-654">Min: `1`</span></span><br><span data-ttu-id="bff5c-655">Biçimlendir `100`</span><span class="sxs-lookup"><span data-stu-id="bff5c-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="bff5c-656">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-656">Required string attribute.</span></span></p><p><span data-ttu-id="bff5c-657">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="bff5c-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="bff5c-658">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-658">Relative paths are supported.</span></span> <span data-ttu-id="bff5c-659">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="bff5c-660">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-661">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="bff5c-662">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="bff5c-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="bff5c-663">Varsayılanını `10`</span><span class="sxs-lookup"><span data-stu-id="bff5c-663">Default: `10`</span></span><br><span data-ttu-id="bff5c-664">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-664">Min: `0`</span></span><br><span data-ttu-id="bff5c-665">Biçimlendir `100`</span><span class="sxs-lookup"><span data-stu-id="bff5c-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="bff5c-666">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="bff5c-667">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="bff5c-668">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="bff5c-669">Varsayılanını `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-669">Default: `00:02:00`</span></span><br><span data-ttu-id="bff5c-670">Min `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-670">Min: `00:00:00`</span></span><br><span data-ttu-id="bff5c-671">Biçimlendir `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="bff5c-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="bff5c-672">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-673">*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="bff5c-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="bff5c-674">Varsayılanını `10`</span><span class="sxs-lookup"><span data-stu-id="bff5c-674">Default: `10`</span></span><br><span data-ttu-id="bff5c-675">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-675">Min: `0`</span></span><br><span data-ttu-id="bff5c-676">Biçimlendir `600`</span><span class="sxs-lookup"><span data-stu-id="bff5c-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="bff5c-677">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="bff5c-678">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="bff5c-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="bff5c-679">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="bff5c-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="bff5c-680">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="bff5c-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="bff5c-681">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="bff5c-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="bff5c-682">Varsayılanını `120`</span><span class="sxs-lookup"><span data-stu-id="bff5c-682">Default: `120`</span></span><br><span data-ttu-id="bff5c-683">Min `0`</span><span class="sxs-lookup"><span data-stu-id="bff5c-683">Min: `0`</span></span><br><span data-ttu-id="bff5c-684">Biçimlendir `3600`</span><span class="sxs-lookup"><span data-stu-id="bff5c-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="bff5c-685">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="bff5c-686">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile** içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="bff5c-687">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="bff5c-687">Optional string attribute.</span></span></p><p><span data-ttu-id="bff5c-688">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="bff5c-689">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="bff5c-690">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="bff5c-691">Modülün günlük dosyasını oluşturması için yolda sunulan klasörlerin bulunması gerekir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="bff5c-692">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="bff5c-693">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="bff5c-694">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="bff5c-694">Setting environment variables</span></span>

<span data-ttu-id="bff5c-695">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="bff5c-696">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="bff5c-697">Bu bölümde ayarlanan ortam değişkenleri, aynı ada sahip sistem ortam değişkenleri ile çakışıyor.</span><span class="sxs-lookup"><span data-stu-id="bff5c-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="bff5c-698">Bir ortam değişkeni hem *web.config* dosyasında hem de Windows 'un sistem düzeyinde ayarlandıysa, *web.config* dosyasındaki değer, uygulamanın başlamasını önleyen sistem ortam değişkeni değerine (örneğin, `ASPNETCORE_ENVIRONMENT: Development;Development` ) eklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="bff5c-699">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bff5c-699">The following example sets two environment variables.</span></span> <span data-ttu-id="bff5c-700">`ASPNETCORE_ENVIRONMENT` uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="bff5c-701">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="bff5c-702">`CONFIG_DIR` , geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="bff5c-703">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="bff5c-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="bff5c-704">app_offline.htm</span></span>

<span data-ttu-id="bff5c-705">Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="bff5c-706">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="bff5c-707">*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="bff5c-708">*app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="bff5c-709">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="bff5c-709">Start-up error page</span></span>

<span data-ttu-id="bff5c-710">ASP.NET Core modülü arka uç işlemini başlatamaz veya arka uç işlemi başlatılır, ancak yapılandırılmış bağlantı noktasında dinleme başarısız olursa, *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="bff5c-711">Bu sayfayı bastırın ve varsayılan IIS 502 durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="bff5c-712">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="bff5c-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="bff5c-713">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="bff5c-713">Log creation and redirection</span></span>

<span data-ttu-id="bff5c-714">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="bff5c-715">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="bff5c-716">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="bff5c-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="bff5c-717">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="bff5c-718">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="bff5c-719">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="bff5c-720">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="bff5c-721">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bff5c-722">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="bff5c-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="bff5c-723">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="bff5c-724">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="bff5c-725">`stdoutLogFile`Yol *stdout* ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934* dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="bff5c-726">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="bff5c-727">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="bff5c-728">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="bff5c-729">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="bff5c-730">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="bff5c-731">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="bff5c-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="bff5c-732">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="bff5c-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="bff5c-733">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="bff5c-734">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="bff5c-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="bff5c-735">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="bff5c-736">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="bff5c-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="bff5c-737">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="bff5c-738">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="bff5c-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="bff5c-739">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="bff5c-740">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="bff5c-741">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="bff5c-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="bff5c-742">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="bff5c-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="bff5c-743">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="bff5c-744">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="bff5c-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="bff5c-745">IIS paylaşılan yapılandırması kullanırken, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="bff5c-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="bff5c-746">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="bff5c-747">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-747">Run the installer.</span></span>
1. <span data-ttu-id="bff5c-748">Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="bff5c-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="bff5c-749">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="bff5c-750">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="bff5c-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="bff5c-751">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="bff5c-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="bff5c-752">Barındırma sisteminde *%windir%\system32\inetsrv dizinine* gidin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="bff5c-753">*aspnetcore.dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="bff5c-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="bff5c-754">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bff5c-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="bff5c-755">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="bff5c-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="bff5c-756">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp* konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log* olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="bff5c-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="bff5c-757">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="bff5c-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="bff5c-758">Modül</span><span class="sxs-lookup"><span data-stu-id="bff5c-758">Module</span></span>

<span data-ttu-id="bff5c-759">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="bff5c-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="bff5c-760">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="bff5c-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="bff5c-761">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="bff5c-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="bff5c-762">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="bff5c-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="bff5c-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="bff5c-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="bff5c-764">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="bff5c-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="bff5c-765">Şema</span><span class="sxs-lookup"><span data-stu-id="bff5c-765">Schema</span></span>

<span data-ttu-id="bff5c-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="bff5c-766">**IIS**</span></span>

* <span data-ttu-id="bff5c-767">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="bff5c-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="bff5c-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="bff5c-768">**IIS Express**</span></span>

* <span data-ttu-id="bff5c-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="bff5c-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="bff5c-770">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bff5c-770">Configuration</span></span>

<span data-ttu-id="bff5c-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="bff5c-771">**IIS**</span></span>

* <span data-ttu-id="bff5c-772">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="bff5c-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="bff5c-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="bff5c-773">**IIS Express**</span></span>

* <span data-ttu-id="bff5c-774">Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="bff5c-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="bff5c-775">*iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="bff5c-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="bff5c-776">Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bff5c-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bff5c-777">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bff5c-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="bff5c-778">[ASP.NET Core Module başvuru kaynağı [varsayılan dal (ana)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): belirli bir sürümü seçmek için **dal** açılan listesini kullanın (örneğin, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="bff5c-778">[ASP.NET Core Module reference source [default branch (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
