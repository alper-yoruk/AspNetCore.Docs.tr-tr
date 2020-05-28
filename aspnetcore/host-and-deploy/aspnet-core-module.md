---
<span data-ttu-id="97c2c-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-102">'Blazor'</span></span>
- <span data-ttu-id="97c2c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-103">'Identity'</span></span>
- <span data-ttu-id="97c2c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-105">'Razor'</span></span>
- <span data-ttu-id="97c2c-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-module"></a><span data-ttu-id="97c2c-107">ASP.NET Core Modülü</span><span class="sxs-lookup"><span data-stu-id="97c2c-107">ASP.NET Core Module</span></span>

<span data-ttu-id="97c2c-108">, [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris](https://github.com/Tratcher)No, [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)ve ka [kotalık](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="97c2c-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="97c2c-109">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="97c2c-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="97c2c-110">`w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="97c2c-111">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="97c2c-112">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="97c2c-112">Supported Windows versions:</span></span>

* <span data-ttu-id="97c2c-113">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="97c2c-113">Windows 7 or later</span></span>
* <span data-ttu-id="97c2c-114">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="97c2c-114">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="97c2c-115">İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="97c2c-116">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-116">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="97c2c-117">Modül, [http. sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="97c2c-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="97c2c-118">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-118">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="97c2c-119">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="97c2c-119">In-process hosting model</span></span>

<span data-ttu-id="97c2c-120">Uygulamalar, işlem içi barındırma modelinde varsayılan olarak ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97c2c-120">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="97c2c-121">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-121">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="97c2c-122">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="97c2c-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="97c2c-123">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="97c2c-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="97c2c-124">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-124">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="97c2c-125">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="97c2c-126">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-126">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="97c2c-127">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="97c2c-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="97c2c-128">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-128">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="97c2c-129">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-129">Use one app pool per app.</span></span>

* <span data-ttu-id="97c2c-130">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtımda el ile bir [app_offline. htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="97c2c-131">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-131">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="97c2c-132">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="97c2c-133">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="97c2c-133">Client disconnects are detected.</span></span> <span data-ttu-id="97c2c-134">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="97c2c-135">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *c:\Windows\System32\inetsrv* için, *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="97c2c-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="97c2c-136">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="97c2c-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="97c2c-137">Yöntemini çağırın `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="97c2c-138"><xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="97c2c-139">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="97c2c-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="97c2c-140">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="97c2c-141">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="97c2c-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="97c2c-142">[Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="97c2c-143">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="97c2c-143">Out-of-process hosting model</span></span>

<span data-ttu-id="97c2c-144">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini `OutOfProcess` Proje dosyasında (*. csproj*) olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="97c2c-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="97c2c-145">İşlem içi barındırma `InProcess` , varsayılan değer olan ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="97c2c-146">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="97c2c-147">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="97c2c-148">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="97c2c-149">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="97c2c-150">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="97c2c-151">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-151">Hosting model changes</span></span>

<span data-ttu-id="97c2c-152">`hostingModel`Ayar *Web. config* dosyasında değiştirilirse ( [Web. config ile yapılandırma](#configuration-with-webconfig) bölümünde açıklanmıştır), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="97c2c-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="97c2c-153">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="97c2c-154">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="97c2c-155">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="97c2c-155">Process name</span></span>

<span data-ttu-id="97c2c-156">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="97c2c-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="97c2c-157">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-157">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="97c2c-158">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="97c2c-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="97c2c-159">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-159">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="97c2c-160">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-160">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="97c2c-161">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="97c2c-162">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="97c2c-162">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="97c2c-163">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="97c2c-163">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="97c2c-164">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="97c2c-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="97c2c-165">Web. config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="97c2c-165">Configuration with web.config</span></span>

<span data-ttu-id="97c2c-166">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *Web. config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="97c2c-167">Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="97c2c-168">Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="97c2c-169"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="97c2c-170">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="97c2c-171">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="97c2c-172">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="97c2c-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="97c2c-173">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-173">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="97c2c-174">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="97c2c-174">Attribute</span></span> | <span data-ttu-id="97c2c-175">Açıklama</span><span class="sxs-lookup"><span data-stu-id="97c2c-175">Description</span></span> | <span data-ttu-id="97c2c-176">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="97c2c-176">Default</span></span> |
| ---
<span data-ttu-id="97c2c-177">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-178">'Blazor'</span></span>
- <span data-ttu-id="97c2c-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-179">'Identity'</span></span>
- <span data-ttu-id="97c2c-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-181">'Razor'</span></span>
- <span data-ttu-id="97c2c-182">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-183">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-184">'Blazor'</span></span>
- <span data-ttu-id="97c2c-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-185">'Identity'</span></span>
- <span data-ttu-id="97c2c-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-187">'Razor'</span></span>
- <span data-ttu-id="97c2c-188">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-188">'SignalR' uid:</span></span> 

<span data-ttu-id="97c2c-189">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-190">'Blazor'</span></span>
- <span data-ttu-id="97c2c-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-191">'Identity'</span></span>
- <span data-ttu-id="97c2c-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-193">'Razor'</span></span>
- <span data-ttu-id="97c2c-194">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-195">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-196">'Blazor'</span></span>
- <span data-ttu-id="97c2c-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-197">'Identity'</span></span>
- <span data-ttu-id="97c2c-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-199">'Razor'</span></span>
- <span data-ttu-id="97c2c-200">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-201">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-202">'Blazor'</span></span>
- <span data-ttu-id="97c2c-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-203">'Identity'</span></span>
- <span data-ttu-id="97c2c-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-205">'Razor'</span></span>
- <span data-ttu-id="97c2c-206">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-206">'SignalR' uid:</span></span> 

<span data-ttu-id="97c2c-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-207">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="97c2c-208">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-208">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-209">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-209">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="97c2c-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-210">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="97c2c-211">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-212">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="97c2c-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-213">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="97c2c-214">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-214">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-215">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="97c2c-216">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="97c2c-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-217">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="97c2c-218">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-218">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-219">Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br><span data-ttu-id="97c2c-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-220">`inprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="97c2c-221">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-222">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="97c2c-223">&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="97c2c-224">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-224">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="97c2c-225">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="97c2c-225">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="97c2c-226">| Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="97c2c-226">| Default: `1`</span></span><br><span data-ttu-id="97c2c-227">Min`1`</span><span class="sxs-lookup"><span data-stu-id="97c2c-227">Min: `1`</span></span><br><span data-ttu-id="97c2c-228">En fazla: `100` &dagger; | |`processPath` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-228">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="97c2c-229">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-229">Required string attribute.</span></span></p><p><span data-ttu-id="97c2c-230">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="97c2c-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="97c2c-231">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-231">Relative paths are supported.</span></span> <span data-ttu-id="97c2c-232">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="97c2c-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-233">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="97c2c-234">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-235">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="97c2c-236">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="97c2c-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="97c2c-237">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-237">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="97c2c-238">| Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="97c2c-238">| Default: `10`</span></span><br><span data-ttu-id="97c2c-239">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-239">Min: `0`</span></span><br><span data-ttu-id="97c2c-240">En fazla: `100` | |`requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-240">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="97c2c-241">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-241">Optional timespan attribute.</span></span></p><p><span data-ttu-id="97c2c-242">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="97c2c-243">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="97c2c-244">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="97c2c-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="97c2c-245">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-245">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="97c2c-246">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="97c2c-247">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="97c2c-248">| Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="97c2c-248">| Default: `00:02:00`</span></span><br><span data-ttu-id="97c2c-249">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="97c2c-249">Min: `00:00:00`</span></span><br><span data-ttu-id="97c2c-250">En fazla: `360:00:00` | |`shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="97c2c-251">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-251">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-252">*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="97c2c-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="97c2c-253">| Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="97c2c-253">| Default: `10`</span></span><br><span data-ttu-id="97c2c-254">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-254">Min: `0`</span></span><br><span data-ttu-id="97c2c-255">En fazla: `600` | |`startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-255">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="97c2c-256">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-256">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-257">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="97c2c-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="97c2c-258">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="97c2c-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="97c2c-259">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="97c2c-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="97c2c-260">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="97c2c-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="97c2c-261">| Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="97c2c-261">| Default: `120`</span></span><br><span data-ttu-id="97c2c-262">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-262">Min: `0`</span></span><br><span data-ttu-id="97c2c-263">En fazla: `3600` | |`stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-263">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="97c2c-264">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-264">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-265">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="97c2c-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-266">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="97c2c-267">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-267">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-268">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="97c2c-269">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-269">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="97c2c-270">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="97c2c-271">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-271">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="97c2c-272">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="97c2c-273">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="97c2c-274">Ortam değişkenlerini belirleme</span><span class="sxs-lookup"><span data-stu-id="97c2c-274">Set environment variables</span></span>

<span data-ttu-id="97c2c-275">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="97c2c-276">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="97c2c-277">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-277">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="97c2c-278">Aşağıdaki örnek, *Web. config*dosyasında iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="97c2c-279">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="97c2c-280">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="97c2c-281">Ortamı doğrudan *Web. config* içinde ayarlamaya alternatif olarak, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil etmek de vardır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="97c2c-282">Bu yaklaşım, proje yayımlandığında *Web. config* içinde ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="97c2c-282">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="97c2c-283">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="97c2c-284">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="97c2c-284">app_offline.htm</span></span>

<span data-ttu-id="97c2c-285">Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="97c2c-286">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="97c2c-287">*App_offline. htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline. htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="97c2c-288">*App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="97c2c-289">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="97c2c-290">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-290">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="97c2c-291">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="97c2c-291">Start-up error page</span></span>

<span data-ttu-id="97c2c-292">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="97c2c-293">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="97c2c-294">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="97c2c-295">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="97c2c-296">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="97c2c-297">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="97c2c-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="97c2c-298">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="97c2c-298">Log creation and redirection</span></span>

<span data-ttu-id="97c2c-299">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="97c2c-300">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="97c2c-301">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="97c2c-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="97c2c-302">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="97c2c-303">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="97c2c-304">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="97c2c-305">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-305">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="97c2c-306">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="97c2c-307">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="97c2c-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="97c2c-308">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="97c2c-309">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="97c2c-310">`stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="97c2c-311">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="97c2c-312">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-312">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="97c2c-313">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="97c2c-314">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="97c2c-315">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="97c2c-316">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="97c2c-317">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="97c2c-318">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="97c2c-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="97c2c-319">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-319">Enhanced diagnostic logs</span></span>

<span data-ttu-id="97c2c-320">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="97c2c-321">`<handlerSettings>`Öğesini `<aspNetCore>` *Web. config*dosyasındaki öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="97c2c-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="97c2c-322">Yoldaki tüm klasörler (önceki örnekteki*Günlükler* ), günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="97c2c-323">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="97c2c-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="97c2c-324">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="97c2c-325">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="97c2c-325">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="97c2c-326">HATA</span><span class="sxs-lookup"><span data-stu-id="97c2c-326">ERROR</span></span>
* <span data-ttu-id="97c2c-327">UYARI</span><span class="sxs-lookup"><span data-stu-id="97c2c-327">WARNING</span></span>
* <span data-ttu-id="97c2c-328">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="97c2c-328">INFO</span></span>
* <span data-ttu-id="97c2c-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="97c2c-329">TRACE</span></span>

<span data-ttu-id="97c2c-330">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="97c2c-330">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="97c2c-331">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="97c2c-331">CONSOLE</span></span>
* <span data-ttu-id="97c2c-332">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="97c2c-332">EVENTLOG</span></span>
* <span data-ttu-id="97c2c-333">DOSYA</span><span class="sxs-lookup"><span data-stu-id="97c2c-333">FILE</span></span>

<span data-ttu-id="97c2c-334">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="97c2c-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="97c2c-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="97c2c-336">(Varsayılan: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="97c2c-336">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="97c2c-337">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="97c2c-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="97c2c-338">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="97c2c-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="97c2c-339">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="97c2c-339">The size of the log isn't limited.</span></span> <span data-ttu-id="97c2c-340">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="97c2c-341">Web. config dosyasındaki öğesinin bir örneği için bkz. [Web. config Ile yapılandırma](#configuration-with-webconfig) `aspNetCore` . *web.config*</span><span class="sxs-lookup"><span data-stu-id="97c2c-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="97c2c-342">Yığın boyutunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="97c2c-342">Modify the stack size</span></span>

<span data-ttu-id="97c2c-343">*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="97c2c-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="97c2c-344">Yönetilen yığın boyutunu, `stackSize` *Web. config*dosyasında bayt cinsinden ayarını kullanarak yapılandırın. Varsayılan boyut bayt 'tır `1048576` (1 MB).</span><span class="sxs-lookup"><span data-stu-id="97c2c-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="97c2c-345">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="97c2c-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="97c2c-346">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="97c2c-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="97c2c-347">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="97c2c-348">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="97c2c-349">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="97c2c-350">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="97c2c-351">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="97c2c-352">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="97c2c-353">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-353">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="97c2c-354">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="97c2c-355">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="97c2c-356">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="97c2c-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="97c2c-357">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="97c2c-358">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="97c2c-359">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="97c2c-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="97c2c-360">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="97c2c-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="97c2c-361">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-361">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="97c2c-362">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-362">Run the installer.</span></span>
1. <span data-ttu-id="97c2c-363">Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-363">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="97c2c-364">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-364">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="97c2c-365">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-365">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="97c2c-366">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="97c2c-366">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="97c2c-367">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="97c2c-368">*Aspnetcore. dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="97c2c-368">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="97c2c-369">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-369">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="97c2c-370">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="97c2c-371">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="97c2c-372">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="97c2c-372">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="97c2c-373">Modül</span><span class="sxs-lookup"><span data-stu-id="97c2c-373">Module</span></span>

<span data-ttu-id="97c2c-374">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="97c2c-374">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="97c2c-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-377">%ProgramFiles%\IIS\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="97c2c-378">% ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="97c2c-379">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="97c2c-379">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="97c2c-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-381">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="97c2c-383">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="97c2c-384">Şema</span><span class="sxs-lookup"><span data-stu-id="97c2c-384">Schema</span></span>

<span data-ttu-id="97c2c-385">**IIS**</span><span class="sxs-lookup"><span data-stu-id="97c2c-385">**IIS**</span></span>

* <span data-ttu-id="97c2c-386">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="97c2c-387">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="97c2c-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="97c2c-388">**IIS Express**</span></span>

* <span data-ttu-id="97c2c-389">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="97c2c-390">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="97c2c-391">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="97c2c-391">Configuration</span></span>

<span data-ttu-id="97c2c-392">**IIS**</span><span class="sxs-lookup"><span data-stu-id="97c2c-392">**IIS**</span></span>

* <span data-ttu-id="97c2c-393">%Windir%\System32\inetsrv\config\applicationHost,config</span><span class="sxs-lookup"><span data-stu-id="97c2c-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="97c2c-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="97c2c-394">**IIS Express**</span></span>

* <span data-ttu-id="97c2c-395">Visual Studio: {APPLICATION ROOT} \\ . Vs\config\applicationhost,config</span><span class="sxs-lookup"><span data-stu-id="97c2c-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="97c2c-396">*ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="97c2c-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="97c2c-397">Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="97c2c-398">ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:</span><span class="sxs-lookup"><span data-stu-id="97c2c-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="97c2c-399">`w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="97c2c-400">Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="97c2c-401">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="97c2c-401">Supported Windows versions:</span></span>

* <span data-ttu-id="97c2c-402">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="97c2c-402">Windows 7 or later</span></span>
* <span data-ttu-id="97c2c-403">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="97c2c-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="97c2c-404">İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="97c2c-405">İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="97c2c-406">Modül, [http. sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="97c2c-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="97c2c-407">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-407">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="97c2c-408">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="97c2c-408">In-process hosting model</span></span>

<span data-ttu-id="97c2c-409">Uygulamayı işlem içi barındırma için yapılandırmak için, `<AspNetCoreHostingModel>` özelliği uygulamanın proje dosyasına `InProcess` (işlem dışı barındırma ile ayarlanır `OutOfProcess` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="97c2c-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="97c2c-410">İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="97c2c-411">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="97c2c-412">`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="97c2c-413">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-413">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="97c2c-414">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="97c2c-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="97c2c-415">İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="97c2c-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="97c2c-416">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-416">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="97c2c-417">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="97c2c-418">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-418">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="97c2c-419">[RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="97c2c-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="97c2c-420">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-420">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="97c2c-421">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-421">Use one app pool per app.</span></span>

* <span data-ttu-id="97c2c-422">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtımda el ile bir [app_offline. htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="97c2c-423">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-423">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="97c2c-424">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="97c2c-425">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="97c2c-425">Client disconnects are detected.</span></span> <span data-ttu-id="97c2c-426">İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="97c2c-427">ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *c:\Windows\System32\inetsrv* için, *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="97c2c-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="97c2c-428">Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="97c2c-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="97c2c-429">Yöntemini çağırın `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-429">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="97c2c-430"><xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="97c2c-431">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="97c2c-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="97c2c-432">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="97c2c-433">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="97c2c-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="97c2c-434">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="97c2c-434">Out-of-process hosting model</span></span>

<span data-ttu-id="97c2c-435">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, proje dosyasında aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="97c2c-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="97c2c-436">`<AspNetCoreHostingModel>`Özelliği belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="97c2c-437">`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="97c2c-438">`<AspNetCoreHostingModel>`Özelliğin değerini olarak ayarlayın `OutOfProcess` (işlem içi barındırma ile ayarlanır `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="97c2c-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="97c2c-439">Değer büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="97c2c-440">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="97c2c-441">İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="97c2c-442">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="97c2c-443">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-443">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="97c2c-444">Barındırma modeli değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-444">Hosting model changes</span></span>

<span data-ttu-id="97c2c-445">`hostingModel`Ayar *Web. config* dosyasında değiştirilirse ( [Web. config ile yapılandırma](#configuration-with-webconfig) bölümünde açıklanmıştır), modül IIS için çalışan işlemini geri dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="97c2c-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="97c2c-446">IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="97c2c-447">Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-447">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="97c2c-448">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="97c2c-448">Process name</span></span>

<span data-ttu-id="97c2c-449">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="97c2c-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="97c2c-450">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-450">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="97c2c-451">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="97c2c-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="97c2c-452">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-452">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="97c2c-453">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-453">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="97c2c-454">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-454">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="97c2c-455">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="97c2c-455">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="97c2c-456">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="97c2c-456">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="97c2c-457">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="97c2c-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="97c2c-458">Web. config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="97c2c-458">Configuration with web.config</span></span>

<span data-ttu-id="97c2c-459">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *Web. config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="97c2c-460">Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="97c2c-461">Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="97c2c-462"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="97c2c-463">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="97c2c-464">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="97c2c-465">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="97c2c-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="97c2c-466">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-466">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="97c2c-467">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="97c2c-467">Attribute</span></span> | <span data-ttu-id="97c2c-468">Açıklama</span><span class="sxs-lookup"><span data-stu-id="97c2c-468">Description</span></span> | <span data-ttu-id="97c2c-469">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="97c2c-469">Default</span></span> |
| ---
<span data-ttu-id="97c2c-470">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-471">'Blazor'</span></span>
- <span data-ttu-id="97c2c-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-472">'Identity'</span></span>
- <span data-ttu-id="97c2c-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-474">'Razor'</span></span>
- <span data-ttu-id="97c2c-475">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-476">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-477">'Blazor'</span></span>
- <span data-ttu-id="97c2c-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-478">'Identity'</span></span>
- <span data-ttu-id="97c2c-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-479">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-480">'Razor'</span></span>
- <span data-ttu-id="97c2c-481">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-481">'SignalR' uid:</span></span> 

<span data-ttu-id="97c2c-482">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-483">'Blazor'</span></span>
- <span data-ttu-id="97c2c-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-484">'Identity'</span></span>
- <span data-ttu-id="97c2c-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-485">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-486">'Razor'</span></span>
- <span data-ttu-id="97c2c-487">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-488">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-489">'Blazor'</span></span>
- <span data-ttu-id="97c2c-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-490">'Identity'</span></span>
- <span data-ttu-id="97c2c-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-491">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-492">'Razor'</span></span>
- <span data-ttu-id="97c2c-493">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-494">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-495">'Blazor'</span></span>
- <span data-ttu-id="97c2c-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-496">'Identity'</span></span>
- <span data-ttu-id="97c2c-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-497">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-498">'Razor'</span></span>
- <span data-ttu-id="97c2c-499">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-499">'SignalR' uid:</span></span> 

<span data-ttu-id="97c2c-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-500">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="97c2c-501">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-501">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-502">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-502">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="97c2c-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-503">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="97c2c-504">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-504">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-505">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="97c2c-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-506">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="97c2c-507">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-507">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-508">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="97c2c-509">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="97c2c-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-510">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="97c2c-511">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-511">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-512">Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br><span data-ttu-id="97c2c-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-513">`outofprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="97c2c-514">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-514">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-515">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="97c2c-516">&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="97c2c-517">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-517">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="97c2c-518">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="97c2c-518">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="97c2c-519">| Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="97c2c-519">| Default: `1`</span></span><br><span data-ttu-id="97c2c-520">Min`1`</span><span class="sxs-lookup"><span data-stu-id="97c2c-520">Min: `1`</span></span><br><span data-ttu-id="97c2c-521">En fazla: `100` &dagger; | |`processPath` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-521">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="97c2c-522">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-522">Required string attribute.</span></span></p><p><span data-ttu-id="97c2c-523">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="97c2c-523">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="97c2c-524">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-524">Relative paths are supported.</span></span> <span data-ttu-id="97c2c-525">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="97c2c-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-526">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="97c2c-527">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-527">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-528">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="97c2c-529">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="97c2c-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="97c2c-530">İşlem içi barındırma ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-530">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="97c2c-531">| Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="97c2c-531">| Default: `10`</span></span><br><span data-ttu-id="97c2c-532">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-532">Min: `0`</span></span><br><span data-ttu-id="97c2c-533">En fazla: `100` | |`requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-533">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="97c2c-534">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-534">Optional timespan attribute.</span></span></p><p><span data-ttu-id="97c2c-535">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="97c2c-536">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="97c2c-537">İşlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="97c2c-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="97c2c-538">İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-538">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="97c2c-539">Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="97c2c-540">Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="97c2c-541">| Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="97c2c-541">| Default: `00:02:00`</span></span><br><span data-ttu-id="97c2c-542">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="97c2c-542">Min: `00:00:00`</span></span><br><span data-ttu-id="97c2c-543">En fazla: `360:00:00` | |`shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="97c2c-544">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-544">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-545">*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="97c2c-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="97c2c-546">| Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="97c2c-546">| Default: `10`</span></span><br><span data-ttu-id="97c2c-547">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-547">Min: `0`</span></span><br><span data-ttu-id="97c2c-548">En fazla: `600` | |`startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-548">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="97c2c-549">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-549">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-550">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="97c2c-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="97c2c-551">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="97c2c-551">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="97c2c-552">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="97c2c-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="97c2c-553">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="97c2c-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="97c2c-554">| Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="97c2c-554">| Default: `120`</span></span><br><span data-ttu-id="97c2c-555">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-555">Min: `0`</span></span><br><span data-ttu-id="97c2c-556">En fazla: `3600` | |`stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-556">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="97c2c-557">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-557">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-558">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="97c2c-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-559">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="97c2c-560">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-560">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-561">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="97c2c-562">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="97c2c-563">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="97c2c-564">Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="97c2c-565">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="97c2c-566">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="97c2c-567">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="97c2c-567">Setting environment variables</span></span>

<span data-ttu-id="97c2c-568">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="97c2c-569">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="97c2c-570">Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="97c2c-571">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-571">The following example sets two environment variables.</span></span> <span data-ttu-id="97c2c-572">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="97c2c-573">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="97c2c-574">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="97c2c-575">Ortamı doğrudan *Web. config* içinde ayarlamaya alternatif olarak, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil etmek de vardır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="97c2c-576">Bu yaklaşım, proje yayımlandığında *Web. config* içinde ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="97c2c-576">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="97c2c-577">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="97c2c-578">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="97c2c-578">app_offline.htm</span></span>

<span data-ttu-id="97c2c-579">Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="97c2c-580">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="97c2c-581">*App_offline. htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline. htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="97c2c-582">*App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="97c2c-583">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="97c2c-584">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-584">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="97c2c-585">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="97c2c-585">Start-up error page</span></span>

<span data-ttu-id="97c2c-586">Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="97c2c-587">ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="97c2c-588">ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="97c2c-589">ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="97c2c-590">Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="97c2c-591">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="97c2c-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="97c2c-592">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="97c2c-592">Log creation and redirection</span></span>

<span data-ttu-id="97c2c-593">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="97c2c-594">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="97c2c-595">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="97c2c-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="97c2c-596">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="97c2c-597">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="97c2c-598">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="97c2c-599">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-599">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="97c2c-600">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="97c2c-601">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="97c2c-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="97c2c-602">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-602">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="97c2c-603">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="97c2c-604">`stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="97c2c-605">`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="97c2c-606">Başlangıçtan sonra tüm ek Günlükler atılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-606">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="97c2c-607">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="97c2c-608">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="97c2c-609">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="97c2c-610">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="97c2c-611">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="97c2c-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="97c2c-612">Gelişmiş tanılama günlükleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-612">Enhanced diagnostic logs</span></span>

<span data-ttu-id="97c2c-613">ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="97c2c-614">`<handlerSettings>`Öğesini `<aspNetCore>` *Web. config*dosyasındaki öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="97c2c-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="97c2c-615">Değer için belirtilen yoldaki klasörler `<handlerSetting>` (önceki örnekteki*Günlükler* ) modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="97c2c-616">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="97c2c-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="97c2c-617">Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="97c2c-618">Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="97c2c-618">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="97c2c-619">HATA</span><span class="sxs-lookup"><span data-stu-id="97c2c-619">ERROR</span></span>
* <span data-ttu-id="97c2c-620">UYARI</span><span class="sxs-lookup"><span data-stu-id="97c2c-620">WARNING</span></span>
* <span data-ttu-id="97c2c-621">BILGISINE</span><span class="sxs-lookup"><span data-stu-id="97c2c-621">INFO</span></span>
* <span data-ttu-id="97c2c-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="97c2c-622">TRACE</span></span>

<span data-ttu-id="97c2c-623">Konumlar (birden çok konuma izin verilir):</span><span class="sxs-lookup"><span data-stu-id="97c2c-623">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="97c2c-624">KONSOLA</span><span class="sxs-lookup"><span data-stu-id="97c2c-624">CONSOLE</span></span>
* <span data-ttu-id="97c2c-625">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="97c2c-625">EVENTLOG</span></span>
* <span data-ttu-id="97c2c-626">DOSYA</span><span class="sxs-lookup"><span data-stu-id="97c2c-626">FILE</span></span>

<span data-ttu-id="97c2c-627">İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-627">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="97c2c-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="97c2c-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="97c2c-629">(Varsayılan: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="97c2c-629">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="97c2c-630">`ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.</span><span class="sxs-lookup"><span data-stu-id="97c2c-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="97c2c-631">Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.**</span><span class="sxs-lookup"><span data-stu-id="97c2c-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="97c2c-632">Günlüğün boyutu sınırlı değil.</span><span class="sxs-lookup"><span data-stu-id="97c2c-632">The size of the log isn't limited.</span></span> <span data-ttu-id="97c2c-633">Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="97c2c-634">Web. config dosyasındaki öğesinin bir örneği için bkz. [Web. config Ile yapılandırma](#configuration-with-webconfig) `aspNetCore` . *web.config*</span><span class="sxs-lookup"><span data-stu-id="97c2c-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="97c2c-635">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="97c2c-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="97c2c-636">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="97c2c-636">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="97c2c-637">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="97c2c-638">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="97c2c-639">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="97c2c-640">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="97c2c-641">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="97c2c-642">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="97c2c-643">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="97c2c-644">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="97c2c-645">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="97c2c-646">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="97c2c-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="97c2c-647">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="97c2c-648">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="97c2c-649">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="97c2c-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="97c2c-650">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="97c2c-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="97c2c-651">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-651">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="97c2c-652">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-652">Run the installer.</span></span>
1. <span data-ttu-id="97c2c-653">Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-653">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="97c2c-654">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-654">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="97c2c-655">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-655">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="97c2c-656">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="97c2c-656">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="97c2c-657">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="97c2c-658">*Aspnetcore. dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="97c2c-658">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="97c2c-659">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-659">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="97c2c-660">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="97c2c-661">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="97c2c-662">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="97c2c-662">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="97c2c-663">Modül</span><span class="sxs-lookup"><span data-stu-id="97c2c-663">Module</span></span>

<span data-ttu-id="97c2c-664">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="97c2c-664">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="97c2c-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-667">%ProgramFiles%\IIS\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="97c2c-668">% ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="97c2c-669">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="97c2c-669">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="97c2c-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-671">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="97c2c-673">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="97c2c-674">Şema</span><span class="sxs-lookup"><span data-stu-id="97c2c-674">Schema</span></span>

<span data-ttu-id="97c2c-675">**IIS**</span><span class="sxs-lookup"><span data-stu-id="97c2c-675">**IIS**</span></span>

* <span data-ttu-id="97c2c-676">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="97c2c-677">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="97c2c-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="97c2c-678">**IIS Express**</span></span>

* <span data-ttu-id="97c2c-679">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="97c2c-680">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema_v2. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="97c2c-681">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="97c2c-681">Configuration</span></span>

<span data-ttu-id="97c2c-682">**IIS**</span><span class="sxs-lookup"><span data-stu-id="97c2c-682">**IIS**</span></span>

* <span data-ttu-id="97c2c-683">%Windir%\System32\inetsrv\config\applicationHost,config</span><span class="sxs-lookup"><span data-stu-id="97c2c-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="97c2c-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="97c2c-684">**IIS Express**</span></span>

* <span data-ttu-id="97c2c-685">Visual Studio: {APPLICATION ROOT} \\ . Vs\config\applicationhost,config</span><span class="sxs-lookup"><span data-stu-id="97c2c-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="97c2c-686">*ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="97c2c-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="97c2c-687">Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="97c2c-688">ASP.NET Core modülü, Web isteklerini arka uca ASP.NET Core uygulamalarına iletmek için IIS ardışık düzenine takılan yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="97c2c-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="97c2c-689">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="97c2c-689">Supported Windows versions:</span></span>

* <span data-ttu-id="97c2c-690">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="97c2c-690">Windows 7 or later</span></span>
* <span data-ttu-id="97c2c-691">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="97c2c-691">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="97c2c-692">Modül yalnızca Kestrel ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-692">The module only works with Kestrel.</span></span> <span data-ttu-id="97c2c-693">Modül, [http. sys](xref:fundamentals/servers/httpsys)ile uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="97c2c-694">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini de işler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="97c2c-695">Modül, ilk istek ulaştığında ASP.NET Core App işlemini başlatır ve kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="97c2c-696">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS 'de işlem içinde çalışan ASP.NET 4. x uygulamaları ile görüldüğü aynı davranıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="97c2c-697">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="97c2c-699">İstekler Web 'den çekirdek modu HTTP. sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="97c2c-700">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="97c2c-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="97c2c-701">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="97c2c-702">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="97c2c-703">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="97c2c-704">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="97c2c-705">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="97c2c-706">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="97c2c-707">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="97c2c-708">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="97c2c-709">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="97c2c-710">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="97c2c-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="97c2c-711">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="97c2c-711">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="97c2c-712">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-712">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="97c2c-713">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-713">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="97c2c-714">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="97c2c-714">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="97c2c-715">ASP.NET Core modülünü yüklemek ve kullanmak</span><span class="sxs-lookup"><span data-stu-id="97c2c-715">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="97c2c-716">ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="97c2c-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="97c2c-717">Web. config ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="97c2c-717">Configuration with web.config</span></span>

<span data-ttu-id="97c2c-718">ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *Web. config* dosyasındaki düğümünün bölümüyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="97c2c-719">Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="97c2c-720">Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="97c2c-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="97c2c-721">Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-721">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="97c2c-722">Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="97c2c-723">IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..</span><span class="sxs-lookup"><span data-stu-id="97c2c-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="97c2c-724">AspNetCore öğesinin öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-724">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="97c2c-725">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="97c2c-725">Attribute</span></span> | <span data-ttu-id="97c2c-726">Açıklama</span><span class="sxs-lookup"><span data-stu-id="97c2c-726">Description</span></span> | <span data-ttu-id="97c2c-727">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="97c2c-727">Default</span></span> |
| ---
<span data-ttu-id="97c2c-728">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-729">'Blazor'</span></span>
- <span data-ttu-id="97c2c-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-730">'Identity'</span></span>
- <span data-ttu-id="97c2c-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-731">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-732">'Razor'</span></span>
- <span data-ttu-id="97c2c-733">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-733">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-734">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-735">'Blazor'</span></span>
- <span data-ttu-id="97c2c-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-736">'Identity'</span></span>
- <span data-ttu-id="97c2c-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-737">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-738">'Razor'</span></span>
- <span data-ttu-id="97c2c-739">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-739">'SignalR' uid:</span></span> 

<span data-ttu-id="97c2c-740">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-741">'Blazor'</span></span>
- <span data-ttu-id="97c2c-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-742">'Identity'</span></span>
- <span data-ttu-id="97c2c-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-743">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-744">'Razor'</span></span>
- <span data-ttu-id="97c2c-745">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-745">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-746">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-747">'Blazor'</span></span>
- <span data-ttu-id="97c2c-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-748">'Identity'</span></span>
- <span data-ttu-id="97c2c-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-749">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-750">'Razor'</span></span>
- <span data-ttu-id="97c2c-751">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-751">'SignalR' uid:</span></span> 

-
<span data-ttu-id="97c2c-752">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="97c2c-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c2c-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-753">'Blazor'</span></span>
- <span data-ttu-id="97c2c-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c2c-754">'Identity'</span></span>
- <span data-ttu-id="97c2c-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c2c-755">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c2c-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c2c-756">'Razor'</span></span>
- <span data-ttu-id="97c2c-757">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="97c2c-757">'SignalR' uid:</span></span> 

<span data-ttu-id="97c2c-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-758">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="97c2c-759">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-759">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-760">**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-760">Arguments to the executable specified in **processPath**.</span></span></p><span data-ttu-id="97c2c-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-761">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="97c2c-762">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-762">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-763">Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="97c2c-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-764">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="97c2c-765">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-765">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-766">True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="97c2c-767">Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="97c2c-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-768">| `true` | | `processesPerApplication` | </span></span><p><span data-ttu-id="97c2c-769">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-769">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-770">**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="97c2c-771">Ayar `processesPerApplication` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-771">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="97c2c-772">Bu öznitelik gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="97c2c-772">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="97c2c-773">| Varsayılanını`1`</span><span class="sxs-lookup"><span data-stu-id="97c2c-773">| Default: `1`</span></span><br><span data-ttu-id="97c2c-774">Min`1`</span><span class="sxs-lookup"><span data-stu-id="97c2c-774">Min: `1`</span></span><br><span data-ttu-id="97c2c-775">En fazla: `100` | |`processPath` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-775">Max: `100` | | `processPath` | </span></span><p><span data-ttu-id="97c2c-776">Gerekli dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-776">Required string attribute.</span></span></p><p><span data-ttu-id="97c2c-777">HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu.</span><span class="sxs-lookup"><span data-stu-id="97c2c-777">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="97c2c-778">Göreli yollar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-778">Relative paths are supported.</span></span> <span data-ttu-id="97c2c-779">Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="97c2c-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-780">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="97c2c-781">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-781">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-782">**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="97c2c-783">Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="97c2c-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> <span data-ttu-id="97c2c-784">| Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="97c2c-784">| Default: `10`</span></span><br><span data-ttu-id="97c2c-785">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-785">Min: `0`</span></span><br><span data-ttu-id="97c2c-786">En fazla: `100` | |`requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-786">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="97c2c-787">İsteğe bağlı TimeSpan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-787">Optional timespan attribute.</span></span></p><p><span data-ttu-id="97c2c-788">ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-788">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="97c2c-789">ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-789">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> <span data-ttu-id="97c2c-790">| Varsayılanını`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="97c2c-790">| Default: `00:02:00`</span></span><br><span data-ttu-id="97c2c-791">Min`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="97c2c-791">Min: `00:00:00`</span></span><br><span data-ttu-id="97c2c-792">En fazla: `360:00:00` | |`shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-792">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="97c2c-793">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-793">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-794">*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="97c2c-794">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="97c2c-795">| Varsayılanını`10`</span><span class="sxs-lookup"><span data-stu-id="97c2c-795">| Default: `10`</span></span><br><span data-ttu-id="97c2c-796">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-796">Min: `0`</span></span><br><span data-ttu-id="97c2c-797">En fazla: `600` | |`startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-797">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="97c2c-798">İsteğe bağlı tamsayı özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-798">Optional integer attribute.</span></span></p><p><span data-ttu-id="97c2c-799">Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre.</span><span class="sxs-lookup"><span data-stu-id="97c2c-799">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="97c2c-800">Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</span><span class="sxs-lookup"><span data-stu-id="97c2c-800">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="97c2c-801">Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</span><span class="sxs-lookup"><span data-stu-id="97c2c-801">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="97c2c-802">0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</span><span class="sxs-lookup"><span data-stu-id="97c2c-802">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="97c2c-803">| Varsayılanını`120`</span><span class="sxs-lookup"><span data-stu-id="97c2c-803">| Default: `120`</span></span><br><span data-ttu-id="97c2c-804">Min`0`</span><span class="sxs-lookup"><span data-stu-id="97c2c-804">Min: `0`</span></span><br><span data-ttu-id="97c2c-805">En fazla: `3600` | |`stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-805">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="97c2c-806">İsteğe bağlı Boolean özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-806">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="97c2c-807">True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-807">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="97c2c-808">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="97c2c-808">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="97c2c-809">İsteğe bağlı dize özniteliği.</span><span class="sxs-lookup"><span data-stu-id="97c2c-809">Optional string attribute.</span></span></p><p><span data-ttu-id="97c2c-810">**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-810">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="97c2c-811">Göreli yollar, sitenin köküne göredir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-811">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="97c2c-812">İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-812">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="97c2c-813">Modülün günlük dosyasını oluşturması için yolda sunulan klasörlerin bulunması gerekir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-813">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="97c2c-814">Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-814">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="97c2c-815">`.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-815">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="97c2c-816">Ortam değişkenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="97c2c-816">Setting environment variables</span></span>

<span data-ttu-id="97c2c-817">Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-817">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="97c2c-818">`<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-818">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="97c2c-819">Bu bölümde ayarlanan ortam değişkenleri, aynı ada sahip sistem ortam değişkenleri ile çakışıyor.</span><span class="sxs-lookup"><span data-stu-id="97c2c-819">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="97c2c-820">Bir ortam değişkeni hem *Web. config* dosyasında hem de Windows 'un sistem düzeyinde ayarlandıysa, *Web. config* dosyasındaki değer, uygulamanın başlamasını önleyen sistem ortam değişkeni değerine (örneğin, `ASPNETCORE_ENVIRONMENT: Development;Development` ) eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-820">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="97c2c-821">Aşağıdaki örnek iki ortam değişkenini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-821">The following example sets two environment variables.</span></span> <span data-ttu-id="97c2c-822">`ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-822">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="97c2c-823">Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-823">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="97c2c-824">`CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-824">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="97c2c-825">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-825">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="97c2c-826">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="97c2c-826">app_offline.htm</span></span>

<span data-ttu-id="97c2c-827">Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-827">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="97c2c-828">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-828">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="97c2c-829">*App_offline. htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline. htm* dosyasının içeriğini geri göndererek isteklere yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-829">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="97c2c-830">*App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-830">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="97c2c-831">Başlatma hatası sayfası</span><span class="sxs-lookup"><span data-stu-id="97c2c-831">Start-up error page</span></span>

<span data-ttu-id="97c2c-832">ASP.NET Core modülü arka uç işlemini başlatamaz veya arka uç işlemi başlatılır, ancak yapılandırılmış bağlantı noktasında dinleme başarısız olursa, *502,5-Işlem hatası* durum kodu sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-832">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="97c2c-833">Bu sayfayı bastırın ve varsayılan IIS 502 durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-833">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="97c2c-834">Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="97c2c-834">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502,5 işlem hatası durum kodu sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="97c2c-836">Günlük oluşturma ve yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="97c2c-836">Log creation and redirection</span></span>

<span data-ttu-id="97c2c-837">ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-837">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="97c2c-838">Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-838">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="97c2c-839">Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).</span><span class="sxs-lookup"><span data-stu-id="97c2c-839">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="97c2c-840">İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-840">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="97c2c-841">Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-841">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="97c2c-842">Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-842">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="97c2c-843">Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-843">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="97c2c-844">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-844">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="97c2c-845">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="97c2c-845">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="97c2c-846">Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-846">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="97c2c-847">Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-847">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="97c2c-848">`stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-848">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="97c2c-849">Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-849">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="97c2c-850">AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-850">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="97c2c-851">Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-851">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="97c2c-852">`%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-852">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="97c2c-853">Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-853">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="97c2c-854">Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="97c2c-854">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="97c2c-855">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="97c2c-855">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="97c2c-856">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-856">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="97c2c-857">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="97c2c-857">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="97c2c-858">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-858">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="97c2c-859">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="97c2c-859">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="97c2c-860">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-860">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="97c2c-861">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="97c2c-861">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="97c2c-862">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-862">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="97c2c-863">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-863">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="97c2c-864">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="97c2c-864">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="97c2c-865">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="97c2c-865">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="97c2c-866">ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-866">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="97c2c-867">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.</span><span class="sxs-lookup"><span data-stu-id="97c2c-867">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="97c2c-868">IIS paylaşılan yapılandırması kullanırken, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="97c2c-868">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="97c2c-869">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-869">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="97c2c-870">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-870">Run the installer.</span></span>
1. <span data-ttu-id="97c2c-871">Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="97c2c-871">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="97c2c-872">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-872">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="97c2c-873">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="97c2c-873">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="97c2c-874">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="97c2c-874">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="97c2c-875">Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-875">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="97c2c-876">*Aspnetcore. dll* dosyasını bulun.</span><span class="sxs-lookup"><span data-stu-id="97c2c-876">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="97c2c-877">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="97c2c-877">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="97c2c-878">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="97c2c-878">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="97c2c-879">Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="97c2c-879">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="97c2c-880">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="97c2c-880">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="97c2c-881">Modül</span><span class="sxs-lookup"><span data-stu-id="97c2c-881">Module</span></span>

<span data-ttu-id="97c2c-882">**IIS (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="97c2c-882">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="97c2c-883">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-883">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="97c2c-885">**IIS Express (X86/AMD64):**</span><span class="sxs-lookup"><span data-stu-id="97c2c-885">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="97c2c-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="97c2c-887">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="97c2c-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="97c2c-888">Şema</span><span class="sxs-lookup"><span data-stu-id="97c2c-888">Schema</span></span>

<span data-ttu-id="97c2c-889">**IIS**</span><span class="sxs-lookup"><span data-stu-id="97c2c-889">**IIS**</span></span>

* <span data-ttu-id="97c2c-890">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="97c2c-891">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="97c2c-891">**IIS Express**</span></span>

* <span data-ttu-id="97c2c-892">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. xml</span><span class="sxs-lookup"><span data-stu-id="97c2c-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="97c2c-893">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="97c2c-893">Configuration</span></span>

<span data-ttu-id="97c2c-894">**IIS**</span><span class="sxs-lookup"><span data-stu-id="97c2c-894">**IIS**</span></span>

* <span data-ttu-id="97c2c-895">%Windir%\System32\inetsrv\config\applicationHost,config</span><span class="sxs-lookup"><span data-stu-id="97c2c-895">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="97c2c-896">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="97c2c-896">**IIS Express**</span></span>

* <span data-ttu-id="97c2c-897">Visual Studio: {APPLICATION ROOT} \\ . Vs\config\applicationhost,config</span><span class="sxs-lookup"><span data-stu-id="97c2c-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="97c2c-898">*ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="97c2c-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="97c2c-899">Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="97c2c-899">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="97c2c-900">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="97c2c-900">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="97c2c-901">[ASP.NET Core modülü başvuru kaynağı (ana dal)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): belirli bir sürümü seçmek için **dal** açılan listesini kullanın (örneğin, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="97c2c-901">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
