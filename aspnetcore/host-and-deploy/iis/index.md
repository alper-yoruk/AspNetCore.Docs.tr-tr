---
<span data-ttu-id="4e35b-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-102">'Blazor'</span></span>
- <span data-ttu-id="4e35b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-103">'Identity'</span></span>
- <span data-ttu-id="4e35b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-105">'Razor'</span></span>
- <span data-ttu-id="4e35b-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="4e35b-107">IIS ile Windows üzerinde ASP.NET Core barındırma</span><span class="sxs-lookup"><span data-stu-id="4e35b-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e35b-108">ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="4e35b-109">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="4e35b-110">Desteklenen işletim sistemleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-110">Supported operating systems</span></span>

<span data-ttu-id="4e35b-111">Aşağıdaki işletim sistemleri desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="4e35b-112">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-112">Windows 7 or later</span></span>
* <span data-ttu-id="4e35b-113">Windows Server 2012 R2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="4e35b-114">[Http. sys sunucusu](xref:fundamentals/servers/httpsys) (eskiden webListener olarak adlandırılır), IIS ile ters proxy yapılandırmasında çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="4e35b-115">[Kestrel sunucusunu](xref:fundamentals/servers/kestrel)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4e35b-116">Azure 'da barındırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/azure-apps/index> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="4e35b-117">Sorun giderme kılavuzu için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="4e35b-118">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="4e35b-118">Supported platforms</span></span>

<span data-ttu-id="4e35b-119">32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="4e35b-120">Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:</span><span class="sxs-lookup"><span data-stu-id="4e35b-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="4e35b-121">64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="4e35b-122">Daha büyük IIS yığın boyutunu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="4e35b-123">64 bitlik yerel bağımlılıklara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="4e35b-124">32 bit (x86) için yayımlanan uygulamalarda IIS uygulama havuzları için 32 bit etkinleştirilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="4e35b-125">Daha fazla bilgi için [IIS sitesini oluşturma](#create-the-iis-site) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="4e35b-126">64 bit uygulama yayımlamak için 64 bit (x64) .NET Core SDK kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="4e35b-127">Ana bilgisayar sisteminde 64 bit çalışma zamanı bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="4e35b-128">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4e35b-129">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="4e35b-129">In-process hosting model</span></span>

<span data-ttu-id="4e35b-130">ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="4e35b-131">İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="4e35b-132">IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e35b-133">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="4e35b-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="4e35b-134">Uygulama başlatmayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-134">Performs app initialization.</span></span>
  * <span data-ttu-id="4e35b-135">[CoreCLR](/dotnet/standard/glossary#coreclr)'yi yükler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="4e35b-136">Çağırır `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="4e35b-137">IIS yerel isteğinin ömrünü işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="4e35b-138">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve süreçte barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![İşlem içi barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="4e35b-140">Web 'den çekirdek modu HTTP. sys sürücüsüne bir istek ulaşır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="4e35b-141">Sürücü, yerel isteği Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4e35b-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="4e35b-142">ASP.NET Core modülü yerel isteği alır ve IIS HTTP sunucusuna ( `IISHttpServer` ) geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="4e35b-143">IIS HTTP sunucusu, isteği yerelden yönetilene dönüştüren bir IIS için işlem içi sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="4e35b-144">IIS HTTP sunucusu isteği tamamladıktan sonra:</span><span class="sxs-lookup"><span data-stu-id="4e35b-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="4e35b-145">İstek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="4e35b-146">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="4e35b-147">Uygulamanın yanıtı IIS HTTP sunucusu aracılığıyla IIS 'e geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="4e35b-148">IIS yanıtı, isteği başlatan istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="4e35b-149">İşlem içi barındırma, mevcut uygulamalar için kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="4e35b-150">ASP.NET Core Web şablonları, işlem içi barındırma modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="4e35b-151">`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.Server.IServer> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> [CoreCLR](/dotnet/standard/glossary#coreclr) 'YI önyüklemek ve uygulamayı IIS çalışan işleminin (*W3wp. exe* veya *iisexpress. exe*) içinde barındırmak için yöntemini çağırarak bir örnek ekler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="4e35b-152">Performans testleri, bir .NET Core uygulamasını işlem içinde barındırmanın, uygulamayı işlem dışı ve [Kestrel](xref:fundamentals/servers/kestrel)'e yönelik proxy istekleri barındırmakla karşılaştırıldığında önemli ölçüde daha yüksek istek aktarım hızı sağladığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4e35b-153">Tek bir dosya yürütülebilir dosyası olarak yayınlanan uygulamalar, işlem içi barındırma modeliyle yüklenemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4e35b-154">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="4e35b-154">Out-of-process hosting model</span></span>

<span data-ttu-id="4e35b-155">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, ASP.NET Core modülü işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="4e35b-156">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="4e35b-157">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e35b-158">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![İşlem dışı barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="4e35b-160">İstekler Web 'den çekirdek modu HTTP. sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="4e35b-161">Sürücü, Web sitesinin yapılandırılmış bağlantı noktasında istekleri IIS 'ye yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="4e35b-162">Yapılandırılmış bağlantı noktası genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4e35b-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="4e35b-163">Modül, isteği uygulama için rastgele bir bağlantı noktasında Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="4e35b-164">Rastgele bağlantı noktası 80 veya 443 değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="4e35b-165">ASP.NET Core modülü başlatma sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="4e35b-166"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Uzantı, sunucuyu dinlemek üzere yapılandırır `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="4e35b-167">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4e35b-168">Modül HTTPS iletmeyi desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="4e35b-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="4e35b-169">İstekler, HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4e35b-170">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine iletilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e35b-171">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e35b-172">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4e35b-173">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri iletilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4e35b-174">ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4e35b-175">Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4e35b-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="4e35b-176">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="4e35b-177">Iısıntegration bileşenlerini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="4e35b-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="4e35b-178">`CreateHostBuilder`(*Program.cs*) içinde BIR konak oluştururken <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> IIS tümleştirmesini etkinleştirmek için çağırın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="4e35b-179">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/generic-host#default-builder-settings> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="4e35b-180">IIS seçenekleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-180">IIS options</span></span>

<span data-ttu-id="4e35b-181">**İşlem içi barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="4e35b-181">**In-process hosting model**</span></span>

<span data-ttu-id="4e35b-182">IIS sunucu seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISServerOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="4e35b-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e35b-183">Aşağıdaki örnek, Automatıcauthentication 'ı devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="4e35b-184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="4e35b-184">Option</span></span>                         | <span data-ttu-id="4e35b-185">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="4e35b-185">Default</span></span> | <span data-ttu-id="4e35b-186">Ayar</span><span class="sxs-lookup"><span data-stu-id="4e35b-186">Setting</span></span> |
| ---
<span data-ttu-id="4e35b-187">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-188">'Blazor'</span></span>
- <span data-ttu-id="4e35b-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-189">'Identity'</span></span>
- <span data-ttu-id="4e35b-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-191">'Razor'</span></span>
- <span data-ttu-id="4e35b-192">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-193">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-194">'Blazor'</span></span>
- <span data-ttu-id="4e35b-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-195">'Identity'</span></span>
- <span data-ttu-id="4e35b-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-197">'Razor'</span></span>
- <span data-ttu-id="4e35b-198">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-199">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-200">'Blazor'</span></span>
- <span data-ttu-id="4e35b-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-201">'Identity'</span></span>
- <span data-ttu-id="4e35b-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-203">'Razor'</span></span>
- <span data-ttu-id="4e35b-204">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-205">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-206">'Blazor'</span></span>
- <span data-ttu-id="4e35b-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-207">'Identity'</span></span>
- <span data-ttu-id="4e35b-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-209">'Razor'</span></span>
- <span data-ttu-id="4e35b-210">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-211">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-212">'Blazor'</span></span>
- <span data-ttu-id="4e35b-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-213">'Identity'</span></span>
- <span data-ttu-id="4e35b-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-215">'Razor'</span></span>
- <span data-ttu-id="4e35b-216">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-217">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-218">'Blazor'</span></span>
- <span data-ttu-id="4e35b-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-219">'Identity'</span></span>
- <span data-ttu-id="4e35b-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-221">'Razor'</span></span>
- <span data-ttu-id="4e35b-222">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-223">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-224">'Blazor'</span></span>
- <span data-ttu-id="4e35b-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-225">'Identity'</span></span>
- <span data-ttu-id="4e35b-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-227">'Razor'</span></span>
- <span data-ttu-id="4e35b-228">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-229">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-230">'Blazor'</span></span>
- <span data-ttu-id="4e35b-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-231">'Identity'</span></span>
- <span data-ttu-id="4e35b-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-233">'Razor'</span></span>
- <span data-ttu-id="4e35b-234">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-235">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-236">'Blazor'</span></span>
- <span data-ttu-id="4e35b-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-237">'Identity'</span></span>
- <span data-ttu-id="4e35b-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-239">'Razor'</span></span>
- <span data-ttu-id="4e35b-240">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-241">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-242">'Blazor'</span></span>
- <span data-ttu-id="4e35b-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-243">'Identity'</span></span>
- <span data-ttu-id="4e35b-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-245">'Razor'</span></span>
- <span data-ttu-id="4e35b-246">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-247">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-248">'Blazor'</span></span>
- <span data-ttu-id="4e35b-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-249">'Identity'</span></span>
- <span data-ttu-id="4e35b-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-251">'Razor'</span></span>
- <span data-ttu-id="4e35b-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-253">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-254">'Blazor'</span></span>
- <span data-ttu-id="4e35b-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-255">'Identity'</span></span>
- <span data-ttu-id="4e35b-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-257">'Razor'</span></span>
- <span data-ttu-id="4e35b-258">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-259">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-260">'Blazor'</span></span>
- <span data-ttu-id="4e35b-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-261">'Identity'</span></span>
- <span data-ttu-id="4e35b-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-263">'Razor'</span></span>
- <span data-ttu-id="4e35b-264">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-264">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-265">--------------- | :-----: | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-266">'Blazor'</span></span>
- <span data-ttu-id="4e35b-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-267">'Identity'</span></span>
- <span data-ttu-id="4e35b-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-269">'Razor'</span></span>
- <span data-ttu-id="4e35b-270">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-270">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-271">---- | | `AutomaticAuthentication`      | `true`  | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-272">İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e35b-273">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e35b-274">Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-275">| | `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="4e35b-276">| | `AllowSynchronousIO`           | `false` | Ve için zaman uyumlu g/ç 'ye izin verilip verilmeyeceğini belirtir `HttpContext.Request` `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="4e35b-277">| | `MaxRequestBodySize`           | `30000000`  | İçin en büyük istek gövdesi boyutunu alır veya ayarlar `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="4e35b-278">IIS 'nin `maxAllowedContentLength` , içindeki kümesinden önce işlenecek sınıra sahip olduğunu unutmayın `MaxRequestBodySize` `IISServerOptions` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="4e35b-279">' Nin değiştirilmesi `MaxRequestBodySize` , etkilemez `maxAllowedContentLength` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="4e35b-280">Arttırmak için `maxAllowedContentLength` , *Web. config* dosyasına `maxAllowedContentLength` daha yüksek bir değere ayarlanacak bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="4e35b-281">Daha fazla ayrıntı için bkz. [yapılandırma](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="4e35b-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="4e35b-282">**İşlem dışı barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="4e35b-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="4e35b-283">IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="4e35b-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e35b-284">Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="4e35b-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="4e35b-285">Seçenek</span><span class="sxs-lookup"><span data-stu-id="4e35b-285">Option</span></span>                         | <span data-ttu-id="4e35b-286">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="4e35b-286">Default</span></span> | <span data-ttu-id="4e35b-287">Ayar</span><span class="sxs-lookup"><span data-stu-id="4e35b-287">Setting</span></span> |
| ---
<span data-ttu-id="4e35b-288">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-289">'Blazor'</span></span>
- <span data-ttu-id="4e35b-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-290">'Identity'</span></span>
- <span data-ttu-id="4e35b-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-292">'Razor'</span></span>
- <span data-ttu-id="4e35b-293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-294">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-295">'Blazor'</span></span>
- <span data-ttu-id="4e35b-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-296">'Identity'</span></span>
- <span data-ttu-id="4e35b-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-298">'Razor'</span></span>
- <span data-ttu-id="4e35b-299">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-300">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-301">'Blazor'</span></span>
- <span data-ttu-id="4e35b-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-302">'Identity'</span></span>
- <span data-ttu-id="4e35b-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-304">'Razor'</span></span>
- <span data-ttu-id="4e35b-305">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-306">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-307">'Blazor'</span></span>
- <span data-ttu-id="4e35b-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-308">'Identity'</span></span>
- <span data-ttu-id="4e35b-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-310">'Razor'</span></span>
- <span data-ttu-id="4e35b-311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-312">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-313">'Blazor'</span></span>
- <span data-ttu-id="4e35b-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-314">'Identity'</span></span>
- <span data-ttu-id="4e35b-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-316">'Razor'</span></span>
- <span data-ttu-id="4e35b-317">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-318">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-319">'Blazor'</span></span>
- <span data-ttu-id="4e35b-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-320">'Identity'</span></span>
- <span data-ttu-id="4e35b-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-322">'Razor'</span></span>
- <span data-ttu-id="4e35b-323">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-324">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-325">'Blazor'</span></span>
- <span data-ttu-id="4e35b-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-326">'Identity'</span></span>
- <span data-ttu-id="4e35b-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-328">'Razor'</span></span>
- <span data-ttu-id="4e35b-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-330">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-331">'Blazor'</span></span>
- <span data-ttu-id="4e35b-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-332">'Identity'</span></span>
- <span data-ttu-id="4e35b-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-334">'Razor'</span></span>
- <span data-ttu-id="4e35b-335">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-336">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-337">'Blazor'</span></span>
- <span data-ttu-id="4e35b-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-338">'Identity'</span></span>
- <span data-ttu-id="4e35b-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-340">'Razor'</span></span>
- <span data-ttu-id="4e35b-341">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-342">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-343">'Blazor'</span></span>
- <span data-ttu-id="4e35b-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-344">'Identity'</span></span>
- <span data-ttu-id="4e35b-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-346">'Razor'</span></span>
- <span data-ttu-id="4e35b-347">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-348">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-349">'Blazor'</span></span>
- <span data-ttu-id="4e35b-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-350">'Identity'</span></span>
- <span data-ttu-id="4e35b-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-352">'Razor'</span></span>
- <span data-ttu-id="4e35b-353">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-354">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-355">'Blazor'</span></span>
- <span data-ttu-id="4e35b-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-356">'Identity'</span></span>
- <span data-ttu-id="4e35b-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-358">'Razor'</span></span>
- <span data-ttu-id="4e35b-359">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-360">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-361">'Blazor'</span></span>
- <span data-ttu-id="4e35b-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-362">'Identity'</span></span>
- <span data-ttu-id="4e35b-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-364">'Razor'</span></span>
- <span data-ttu-id="4e35b-365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-365">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-366">--------------- | :-----: | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-367">'Blazor'</span></span>
- <span data-ttu-id="4e35b-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-368">'Identity'</span></span>
- <span data-ttu-id="4e35b-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-370">'Razor'</span></span>
- <span data-ttu-id="4e35b-371">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-371">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-372">---- | | `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-373">İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e35b-374">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e35b-375">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="4e35b-376">| | `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="4e35b-377">| | `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4e35b-378">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="4e35b-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4e35b-379">[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) ve ASP.NET Core modülü, iletmek üzere yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="4e35b-380">Düzen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4e35b-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="4e35b-381">İsteğin kaynaklandığı uzak IP adresi.</span><span class="sxs-lookup"><span data-stu-id="4e35b-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="4e35b-382">[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , Iletilen üstbilgiler ara yazılımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="4e35b-383">Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="4e35b-384">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="4e35b-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="4e35b-385">Web. config dosyası</span><span class="sxs-lookup"><span data-stu-id="4e35b-385">web.config file</span></span>

<span data-ttu-id="4e35b-386">*Web. config* dosyası [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e35b-387">*Web. config* dosyası oluşturma, dönüştürme ve yayımlama, proje yayımlandığında bir MSBuild hedefi () tarafından işlenir `_TransformWebConfig` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="4e35b-388">Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="4e35b-389">SDK proje dosyasının en üstünde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4e35b-390">Bir *Web. config* dosyası projede yoksa, dosya ASP.NET Core modülünü yapılandırmak Için doğru *processPath* ve *bağımsız değişkenlerle* oluşturulur ve [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="4e35b-391">Projede bir *Web. config* dosyası varsa, dosya ASP.NET Core modülünü yapılandırmak ve yayımlanan çıktıya taşınmak Için doğru *processPath* ve *bağımsız değişkenlerle* birlikte dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="4e35b-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="4e35b-392">Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="4e35b-393">*Web. config* dosyası, etkin IIS modüllerini DENETLEYEN ek IIS yapılandırma ayarları verebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="4e35b-394">ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.</span><span class="sxs-lookup"><span data-stu-id="4e35b-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="4e35b-395">Web SDK 'sının *Web. config* dosyasını dönüştürülmesini engellemek için, **\<IsTransformWebConfigDisabled>** Proje dosyasındaki özelliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="4e35b-396">Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakıldığında, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="4e35b-397">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="4e35b-398">Web. config dosyası konumu</span><span class="sxs-lookup"><span data-stu-id="4e35b-398">web.config file location</span></span>

<span data-ttu-id="4e35b-399">[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, *Web. config* dosyası dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="4e35b-400">Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="4e35b-401">Web Dağıtımı kullanarak birden çok uygulama yayımlamayı etkinleştirmek için uygulamanın kökünde *Web. config* dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="4e35b-402">Uygulamanın fiziksel yolunda \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . xml* (XML belge açıklamaları) ve \* \<assembly> . Deps. JSON\*gibi hassas dosyalar bulunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="4e35b-403">*Web. config* dosyası mevcut olduğunda ve site normal olarak başlatıldığında, istenirse IIS bu hassas dosyaları sunmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="4e35b-404">*Web. config* dosyası eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="4e35b-405">***Web. config* dosyasının her zaman dağıtımda mevcut olması, doğru şekilde adlandırılması ve siteyi normal başlangıç için yapılandırabiliyor olması gerekir. *Web. config* dosyasını bir üretim dağıtımından hiçbir şekilde kaldırmayın.**</span><span class="sxs-lookup"><span data-stu-id="4e35b-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="4e35b-406">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="4e35b-406">Transform web.config</span></span>

<span data-ttu-id="4e35b-407">Yayımlama sırasında *Web. config* ' i dönüştürmeniz gerekiyorsa bkz <xref:host-and-deploy/iis/transform-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="4e35b-408">Yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak için Publish üzerinde *Web. config* ' i dönüştürmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="4e35b-409">IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-409">IIS configuration</span></span>

<span data-ttu-id="4e35b-410">**Windows Server işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="4e35b-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="4e35b-411">**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="4e35b-412">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi**bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="4e35b-413">**Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="4e35b-415">**Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="4e35b-416">İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="4e35b-418">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e35b-419">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="4e35b-420">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e35b-421">Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e35b-422">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e35b-423">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e35b-424">WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="4e35b-425">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e35b-426">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="4e35b-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e35b-427">Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="4e35b-428">**Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="4e35b-429">**Windows masaüstü işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="4e35b-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="4e35b-430">**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini**etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e35b-431">**Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="4e35b-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="4e35b-432">**Internet Information Services** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="4e35b-433">**Web yönetimi araçları** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="4e35b-434">**IIS Yönetim Konsolu**kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="4e35b-435">**World Wide Web Hizmetleri**kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e35b-436">**World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="4e35b-437">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e35b-438">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="4e35b-439">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e35b-440">Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e35b-441">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e35b-442">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e35b-443">WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="4e35b-444">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e35b-445">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="4e35b-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e35b-446">IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-446">If the IIS installation requires a restart, restart the system.</span></span>

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="4e35b-448">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4e35b-449">*.NET Core barındırma paketi* 'ni barındırma sistemine yükler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="4e35b-450">Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="4e35b-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e35b-451">Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e35b-452">Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="4e35b-453">IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="4e35b-454">.NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="4e35b-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4e35b-455">Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="4e35b-456">Doğrudan indirme (geçerli sürüm)</span><span class="sxs-lookup"><span data-stu-id="4e35b-456">Direct download (current version)</span></span>

<span data-ttu-id="4e35b-457">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="4e35b-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="4e35b-458">Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="4e35b-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="4e35b-459">Yükleyicinin önceki sürümleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-459">Earlier versions of the installer</span></span>

<span data-ttu-id="4e35b-460">Yükleyicinin önceki bir sürümünü elde etmek için:</span><span class="sxs-lookup"><span data-stu-id="4e35b-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="4e35b-461">[.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="4e35b-462">İstediğiniz .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="4e35b-463">**Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="4e35b-464">**Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="4e35b-465">Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="4e35b-466">Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="4e35b-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="4e35b-467">Barındırma paketini yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="4e35b-468">Yükleyiciyi sunucuda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-468">Run the installer on the server.</span></span> <span data-ttu-id="4e35b-469">Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="4e35b-470">`OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="4e35b-471">`OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="4e35b-472">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e35b-473">`OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="4e35b-474">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e35b-475">`OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="4e35b-476">32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="4e35b-477">Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="4e35b-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma (*ApplicationHost. config*) IIS yüklemesiyle aynı makinada olduğunda, IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="4e35b-479">*Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="4e35b-480">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="4e35b-481">Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="4e35b-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="4e35b-482">IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.</span><span class="sxs-lookup"><span data-stu-id="4e35b-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="4e35b-483">ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için geri iletme davranışını benimsemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="4e35b-484">Yeni bir barındırma paketi yükleyerek paylaşılan çerçeveyi yükselttikten sonra, sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="4e35b-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="4e35b-485">IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="4e35b-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="4e35b-486">Visual Studio ile yayımlarken Web Dağıtımı yüklemesi</span><span class="sxs-lookup"><span data-stu-id="4e35b-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="4e35b-487">Uygulamaları [Web dağıtımı](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)olan sunuculara dağıttığınızda, en son Web dağıtımı sürümünü sunucuya yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="4e35b-488">Web Dağıtımı yüklemek için [Web Platformu Yükleyicisi 'ni (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft İndirme Merkezi](https://www.microsoft.com/download/details.aspx?id=43717)'nden bir yükleyici edinin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="4e35b-489">Tercih edilen yöntem, WebPI kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="4e35b-490">WebPI, barındırma sağlayıcıları için tek başına kurulum ve yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="4e35b-491">IIS sitesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="4e35b-491">Create the IIS site</span></span>

1. <span data-ttu-id="4e35b-492">Barındırma sisteminde, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="4e35b-493">Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="4e35b-494">Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="4e35b-495">IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="4e35b-496">**Siteler** klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="4e35b-497">Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="4e35b-498">Bir **site adı** belirtin ve **fiziksel yolu** uygulamanın dağıtım klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="4e35b-499">**Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Web sitesi Ekle adımında site adı, fiziksel yol ve ana bilgisayar adını sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="4e35b-501">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="4e35b-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4e35b-502">Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="4e35b-503">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4e35b-504">Joker karakterler yerine açık ana bilgisayar adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="4e35b-505">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4e35b-506">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="4e35b-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="4e35b-507">Sunucu düğümünün altında **uygulama havuzları**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="4e35b-508">Sitenin uygulama havuzuna sağ tıklayın ve bağlam menüsünden **temel ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="4e35b-509">**Uygulama havuzunu Düzenle** penceresinde, **.NET CLR sürümünü** **yönetilen kod olmadan**ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR sürümü için yönetilen kod ayarlama.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="4e35b-511">ASP.NET Core ayrı bir işlemde çalışır ve çalışma zamanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="4e35b-512">ASP.NET Core, masaüstü CLR 'yi (.NET CLR) yüklemeye bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="4e35b-513">.NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı çalışan işlemde barındırmak için önyüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="4e35b-514">**.NET CLR sürümünün** **yönetilen kod olmadan** ayarlanması isteğe bağlıdır, ancak önerilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="4e35b-515">*ASP.NET Core 2,2 veya üzeri*:</span><span class="sxs-lookup"><span data-stu-id="4e35b-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="4e35b-516">[İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 32 bit SDK ile yayımlanmış 32-bit (x86) bir [dağıtımı](/dotnet/core/deploying/#self-contained-deployments-scd) için, uygulama havuzunu 32 bit olarak etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="4e35b-517">IIS Yöneticisi 'nde **Bağlantılar** kenar çubuğunda **uygulama havuzları** ' na gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="4e35b-518">Uygulamanın uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-518">Select the app's Application Pool.</span></span> <span data-ttu-id="4e35b-519">**Eylemler** kenar çubuğunda **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="4e35b-520">**32 bitlik uygulamaları etkinleştir** olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="4e35b-521">[İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bitlik (x64) bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, 32 bit (x86) işlemleri için uygulama havuzunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="4e35b-522">IIS Yöneticisi 'nde **Bağlantılar** kenar çubuğunda **uygulama havuzları** ' na gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="4e35b-523">Uygulamanın uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-523">Select the app's Application Pool.</span></span> <span data-ttu-id="4e35b-524">**Eylemler** kenar çubuğunda **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="4e35b-525">**32 bitlik uygulamaları etkinleştir** olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="4e35b-526">İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="4e35b-527">Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) **applicationpokaydentity** 'den başka bir kimliğe değiştiyse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="4e35b-528">Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="4e35b-529">**Windows kimlik doğrulama yapılandırması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="4e35b-530">Daha fazla bilgi için bkz. [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="4e35b-531">Uygulamayı dağıtma</span><span class="sxs-lookup"><span data-stu-id="4e35b-531">Deploy the app</span></span>

<span data-ttu-id="4e35b-532">Uygulamayı [IIS sitesi oluşturma](#create-the-iis-site) bölümünde oluşturulan IIS **fiziksel yol** klasörüne dağıtın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="4e35b-533">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *Publish* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="4e35b-534">Visual Studio ile Web Dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="4e35b-535">Web Dağıtımı kullanılacak bir yayımlama profili oluşturma hakkında bilgi edinmek için bkz. [ASP.NET Core App Deployment Için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="4e35b-536">Barındırma sağlayıcısı, bir yayımlama profili veya oluşturma desteği sağlıyorsa, profilini indirin ve Visual Studio **Yayımlama** iletişim kutusunu kullanarak içeri aktarın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Yayımla iletişim sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="4e35b-538">Visual Studio dışında Web Dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="4e35b-539">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) , komut satırından Visual Studio dışında da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="4e35b-540">Daha fazla bilgi için bkz. [Web Dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="4e35b-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="4e35b-541">Web Dağıtımı alternatifleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="4e35b-542">Uygulamayı barındırma sistemine taşımak için el ile kopyalama, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi çeşitli yöntemlerden birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="4e35b-543">IIS 'ye dağıtım ASP.NET Core hakkında daha fazla bilgi için, [IIS yöneticileri Için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="4e35b-544">Web sitesine gidin</span><span class="sxs-lookup"><span data-stu-id="4e35b-544">Browse the website</span></span>

<span data-ttu-id="4e35b-545">Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın genel uç noktalarından birine bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="4e35b-546">Aşağıdaki örnekte, site bağlantı noktasındaki IIS **ana bilgisayar adına** bağlıdır `www.mysite.com` **Port** `80` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="4e35b-547">Bir istek şu şekilde yapılır `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="4e35b-547">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge tarayıcısı, IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="4e35b-549">Kilitli dağıtım dosyaları</span><span class="sxs-lookup"><span data-stu-id="4e35b-549">Locked deployment files</span></span>

<span data-ttu-id="4e35b-550">Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="4e35b-551">Dağıtım sırasında kilitli dosyaların üzerine yazılamaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="4e35b-552">Kilitli dosyaları bir dağıtımda serbest bırakmak için aşağıdaki yaklaşımlardan **birini** kullanarak uygulama havuzunu durdurun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="4e35b-553">Proje dosyasında Web Dağıtımı ve başvurusunu kullanın `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="4e35b-554">Bir *app_offline. htm* dosyası Web uygulaması dizininin köküne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="4e35b-555">Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="4e35b-556">Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="4e35b-557">Sunucu üzerindeki IIS Yöneticisi 'nde uygulama havuzunu el ile durdurun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="4e35b-558">*App_offline. htm* dosyasını bırakmak için PowerShell kullanın (PowerShell 5 veya üzerini gerektirir):</span><span class="sxs-lookup"><span data-stu-id="4e35b-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="4e35b-559">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="4e35b-559">Data protection</span></span>

<span data-ttu-id="4e35b-560">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="4e35b-561">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="4e35b-562">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="4e35b-563">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="4e35b-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="4e35b-564">Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçleri geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="4e35b-565">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="4e35b-566">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="4e35b-567">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata)içerebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="4e35b-568">Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="4e35b-569">**Veri koruma kayıt defteri anahtarları oluşturma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="4e35b-570">ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="4e35b-571">Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="4e35b-572">Tek başına, Web grubu olmayan IIS yüklemeleri için [Data Protection provision-AutoGenKeys. ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="4e35b-573">Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="4e35b-574">Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="4e35b-575">Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="4e35b-576">Varsayılan olarak, veri koruma anahtarları şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="4e35b-577">Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştırıldığı Windows hesabıyla sınırlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="4e35b-578">Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="4e35b-579">Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün: sertifikaları kullanıcının güvenilen sertifika deposuna yerleştir ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="4e35b-580">Ayrıntılar için bkz. [ASP.NET Core veri korumasını yapılandırma](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="4e35b-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="4e35b-581">**Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="4e35b-582">Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="4e35b-583">**Kullanıcı profilini yükle** ' ye ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="4e35b-584">Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="4e35b-585">Anahtarlar *% LocalAppData%/ASP.exe. net/DataProtection-Keys* klasörüne kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="4e35b-586">Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="4e35b-587">Varsayılan değeri `setProfileEnvironment` `true` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="4e35b-588">Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="4e35b-589">Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:</span><span class="sxs-lookup"><span data-stu-id="4e35b-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="4e35b-590">*% Windir%/system32/inetsrv/config* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="4e35b-591">*ApplicationHost. config* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="4e35b-592">Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="4e35b-593">`setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="4e35b-594">**Dosya sistemini anahtar halka deposu olarak kullanma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="4e35b-595">[Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="4e35b-596">Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="4e35b-597">Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="4e35b-598">IIS 'yi bir Web grubunda kullanırken:</span><span class="sxs-lookup"><span data-stu-id="4e35b-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="4e35b-599">Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="4e35b-600">Her makineye bir x509 sertifikası dağıtın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="4e35b-601">[Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="4e35b-602">**Veri koruma için makineye özel bir ilke ayarlama**</span><span class="sxs-lookup"><span data-stu-id="4e35b-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="4e35b-603">Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="4e35b-604">Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="4e35b-605">Sanal Dizinler</span><span class="sxs-lookup"><span data-stu-id="4e35b-605">Virtual Directories</span></span>

<span data-ttu-id="4e35b-606">[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="4e35b-607">Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="4e35b-608">Alt uygulamalar</span><span class="sxs-lookup"><span data-stu-id="4e35b-608">Sub-applications</span></span>

<span data-ttu-id="4e35b-609">ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="4e35b-610">Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="4e35b-611">Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="4e35b-612">Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="4e35b-613">Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="4e35b-614">Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="4e35b-615">İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="4e35b-616">Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="4e35b-617">Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="4e35b-618">Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:</span><span class="sxs-lookup"><span data-stu-id="4e35b-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="4e35b-619">Alt uygulama için bir uygulama havuzu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="4e35b-620">.NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="4e35b-621">Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="4e35b-622">IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="4e35b-623">**Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="4e35b-624">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-624">Select **OK**.</span></span>

<span data-ttu-id="4e35b-625">Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="4e35b-626">İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="4e35b-627">Web. config ile IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="4e35b-628">IIS yapılandırması, `<system.webServer>` ASP.NET Core modüllü ASP.NET Core uygulamalar için işlevsel olan IIS senaryoları için *Web. config* 'in bölümü tarafından etkilenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="4e35b-629">Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="4e35b-630">IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulamanın *Web. config* dosyasındaki öğesi ASP.NET Core bir uygulama için devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="4e35b-631">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="4e35b-632">İçin yapılandırma başvurusu\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="4e35b-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="4e35b-633">Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri \<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *Appcmd. exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="4e35b-634">Web. config 'in yapılandırma bölümleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-634">Configuration sections of web.config</span></span>

<span data-ttu-id="4e35b-635">*Web. config* dosyasındaki ASP.NET 4. x uygulamalarının yapılandırma bölümleri yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmaz:</span><span class="sxs-lookup"><span data-stu-id="4e35b-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="4e35b-636">ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="4e35b-637">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4e35b-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="4e35b-638">Uygulama havuzları</span><span class="sxs-lookup"><span data-stu-id="4e35b-638">Application Pools</span></span>

<span data-ttu-id="4e35b-639">Uygulama havuzu yalıtımı, barındırma modeliyle belirlenir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="4e35b-640">İşlem içi barındırma: uygulamaların ayrı uygulama havuzlarında çalışması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="4e35b-641">İşlem dışı barındırma: her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="4e35b-642">IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzu olur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="4e35b-643">Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="4e35b-644">Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="4e35b-645">Uygulama havuzuIdentity</span><span class="sxs-lookup"><span data-stu-id="4e35b-645">Application Pool Identity</span></span>

<span data-ttu-id="4e35b-646">Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="4e35b-647">IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="4e35b-648">Uygulama havuzunun **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu 'nda, uygulamasının **Identity** **applicationpokaydentity**kullanacak şekilde ayarlandığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

<span data-ttu-id="4e35b-650">IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="4e35b-651">Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="4e35b-652">Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="4e35b-653">IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4e35b-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="4e35b-654">Windows Gezgini 'ni açın ve dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="4e35b-655">Dizine sağ tıklayıp **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="4e35b-656">**Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="4e35b-657">**Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="4e35b-658">**Seçilecek nesne adlarını girin** alanına \*\* \\ app_pool_name>IIS AppPool<\*\* girin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="4e35b-659">**Adları denetle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-659">Select the **Check Names** button.</span></span> <span data-ttu-id="4e35b-660">*DefaultAppPool* için **IIS APPPOOL\DefaultAppPool**kullanarak adları denetleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="4e35b-661">**Adları denetle** düğmesi seçildiğinde, nesne adları alanında bir **DefaultAppPool** değeri gösterilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="4e35b-662">Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="4e35b-663">Nesne adı denetlenirken **IIS AppPool \\<APP_POOL_NAME>** biçimini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="4e35b-665">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-665">Select **OK**.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="4e35b-667">Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="4e35b-668">Gerektiğinde ek izinler sağlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="4e35b-669">Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="4e35b-670">Örnek olarak *DefaultAppPool* kullanarak, aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="4e35b-671">Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4e35b-672">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="4e35b-672">HTTP/2 support</span></span>

<span data-ttu-id="4e35b-673">[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="4e35b-674">İşlem içi</span><span class="sxs-lookup"><span data-stu-id="4e35b-674">In-process</span></span>
  * <span data-ttu-id="4e35b-675">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="4e35b-676">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="4e35b-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="4e35b-677">İşlem dışı</span><span class="sxs-lookup"><span data-stu-id="4e35b-677">Out-of-process</span></span>
  * <span data-ttu-id="4e35b-678">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="4e35b-679">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="4e35b-680">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="4e35b-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4e35b-681">Bir HTTP/2 bağlantısı oluşturulduğunda, bir işlem içi dağıtım için, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="4e35b-682">Bir HTTP/2 bağlantısı oluşturulduğunda bir işlem dışı dağıtımı için, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="4e35b-683">İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4e35b-684">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4e35b-685">HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="4e35b-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="4e35b-686">IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4e35b-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="4e35b-687">CORS ön denetim istekleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-687">CORS preflight requests</span></span>

<span data-ttu-id="4e35b-688">*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="4e35b-689">.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="4e35b-690">*Web. config* DOSYASıNDAKI uygulama IIS işleyicilerini seçenek isteklerini geçecek şekilde yapılandırma hakkında bilgi edinmek için bkz. [ASP.NET Web API 2 ' de çapraz kaynak ISTEKLERINI etkinleştirme: CORS 'nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="4e35b-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="4e35b-691">Uygulama başlatma modülü ve boşta kalma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="4e35b-692">ASP.NET Core modülü sürüm 2 tarafından IIS 'de barındırıldığında:</span><span class="sxs-lookup"><span data-stu-id="4e35b-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="4e35b-693">[Uygulama başlatma modülü](#application-initialization-module): uygulamanın çalışan işlem yeniden başlatması veya sunucu yeniden başlatması üzerinde otomatik olarak başlayacak şekilde, [uygulamanın barındırılan veya](#in-process-hosting-model) [işlem dışı](#out-of-process-hosting-model) bir şekilde yapılandırılmış olması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="4e35b-694">[Boşta kalma zaman aşımı](#idle-timeout): uygulamanın şirket [içinde barındırılan işlemi, işlem](#in-process-hosting-model) yapılmayan dönemler sırasında zaman aşımına uğramaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="4e35b-695">Uygulama başlatma modülü</span><span class="sxs-lookup"><span data-stu-id="4e35b-695">Application Initialization Module</span></span>

<span data-ttu-id="4e35b-696">*İşlem içi ve işlem dışı barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="4e35b-697">[IIS uygulaması başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , uygulama havuzu başlatıldığında veya geri DÖNÜŞTÜRÜLDÜĞÜNDE uygulamaya http isteği gönderen bir IIS özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="4e35b-698">İstek, uygulamayı başlatmak üzere tetikler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-698">The request triggers the app to start.</span></span> <span data-ttu-id="4e35b-699">Varsayılan olarak IIS, uygulamayı başlatmak için uygulamanın kök URL 'SI () için bir istek yayınlar `/` (yapılandırma hakkında daha fazla bilgi için [ek kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).</span><span class="sxs-lookup"><span data-stu-id="4e35b-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="4e35b-700">IIS uygulama başlatma rolü özelliğinin etkin olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="4e35b-701">IIS 'yi yerel olarak kullanırken Windows 7 veya üzeri masaüstü sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="4e35b-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="4e35b-702">**Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="4e35b-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="4e35b-703">**Internet Information Services** > **World Wide Web Services** > **uygulama geliştirme özelliklerini**açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="4e35b-704">**Uygulama başlatma**onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="4e35b-705">Windows Server 2008 R2 veya sonraki sürümlerde:</span><span class="sxs-lookup"><span data-stu-id="4e35b-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="4e35b-706">**Rol ve Özellik Ekleme Sihirbazı 'nı**açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="4e35b-707">**Rol hizmetlerini Seç** panelinde, **uygulama geliştirme** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="4e35b-708">**Uygulama başlatma**onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="4e35b-709">Site için uygulama başlatma modülünü etkinleştirmek üzere aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="4e35b-710">IIS Yöneticisi 'Ni kullanma:</span><span class="sxs-lookup"><span data-stu-id="4e35b-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="4e35b-711">**Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="4e35b-712">Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="4e35b-713">Varsayılan **Başlangıç modu** **OnDemand**' dir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="4e35b-714">**Başlangıç modunu** **AlwaysRunning**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="4e35b-715">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-715">Select **OK**.</span></span>
  1. <span data-ttu-id="4e35b-716">**Bağlantılar** panelinde **siteler** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="4e35b-717">Uygulamaya sağ tıklayın ve **Web sitesi** > **Gelişmiş ayarlarını**Yönet ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="4e35b-718">Varsayılan **önyükleme etkin** ayarı **false**şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="4e35b-719">**Önyükleme etkin** ' i **true**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="4e35b-720">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-720">Select **OK**.</span></span>

* <span data-ttu-id="4e35b-721">*Web. config*kullanarak `<applicationInitialization>` öğesini, `doAppInitAfterRestart` `true` `<system.webServer>` uygulamasının *Web. config* dosyasındaki öğelerine ayarlı öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4e35b-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="4e35b-722">Boşta Kalma Süresi Zaman Aşımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-722">Idle Timeout</span></span>

<span data-ttu-id="4e35b-723">*Yalnızca işlem sırasında barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="4e35b-724">Uygulamanın çalışmasını engellemek için, IIS Yöneticisi 'Ni kullanarak uygulama havuzunun boşta kalma zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="4e35b-725">**Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="4e35b-726">Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="4e35b-727">Varsayılan **boşta kalma süresi (dakika)** **20** dakikadır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="4e35b-728">**Boşta kalma zaman aşımını (dakika)** **0** (sıfır) olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="4e35b-729">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-729">Select **OK**.</span></span>
1. <span data-ttu-id="4e35b-730">Çalışan işlemini geri dönüştür.</span><span class="sxs-lookup"><span data-stu-id="4e35b-730">Recycle the worker process.</span></span>

<span data-ttu-id="4e35b-731">[İşlem dışı](#out-of-process-hosting-model) barındırılan uygulamaların zaman aşımına uğramasını engellemek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="4e35b-732">Uygulamayı çalışır durumda tutmak için bir dış hizmetten ping yapın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="4e35b-733">Uygulama yalnızca arka plan hizmetleri barındırıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için bir Windows hizmeti](xref:host-and-deploy/windows-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="4e35b-734">Uygulama başlatma modülü ve boşta kalma zaman aşımı ek kaynakları</span><span class="sxs-lookup"><span data-stu-id="4e35b-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="4e35b-735">IIS 8,0 uygulama başlatma</span><span class="sxs-lookup"><span data-stu-id="4e35b-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="4e35b-736">[Uygulama başlatma \<applicationInitialization> ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="4e35b-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="4e35b-737">[Uygulama havuzu \<processModel> Için Işlem modeli ayarları ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="4e35b-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="4e35b-738">IIS yöneticileri için dağıtım kaynakları</span><span class="sxs-lookup"><span data-stu-id="4e35b-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="4e35b-739">IIS belgeleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="4e35b-740">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="4e35b-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="4e35b-741">.NET Core uygulama dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="4e35b-742">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4e35b-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="4e35b-743">Resmi Microsoft IIS sitesi</span><span class="sxs-lookup"><span data-stu-id="4e35b-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="4e35b-744">Windows Server teknik içerik kitaplığı</span><span class="sxs-lookup"><span data-stu-id="4e35b-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="4e35b-745">IIS üzerinde HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4e35b-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4e35b-746">ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="4e35b-747">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="4e35b-748">Desteklenen işletim sistemleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-748">Supported operating systems</span></span>

<span data-ttu-id="4e35b-749">Aşağıdaki işletim sistemleri desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="4e35b-750">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-750">Windows 7 or later</span></span>
* <span data-ttu-id="4e35b-751">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="4e35b-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4e35b-752">[Http. sys sunucusu](xref:fundamentals/servers/httpsys) (eskiden webListener olarak adlandırılır), IIS ile ters proxy yapılandırmasında çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="4e35b-753">[Kestrel sunucusunu](xref:fundamentals/servers/kestrel)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4e35b-754">Azure 'da barındırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/azure-apps/index> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="4e35b-755">Sorun giderme kılavuzu için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="4e35b-756">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="4e35b-756">Supported platforms</span></span>

<span data-ttu-id="4e35b-757">32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="4e35b-758">Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:</span><span class="sxs-lookup"><span data-stu-id="4e35b-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="4e35b-759">64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="4e35b-760">Daha büyük IIS yığın boyutunu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="4e35b-761">64 bitlik yerel bağımlılıklara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="4e35b-762">64 bit uygulama yayımlamak için 64 bit (x64) .NET Core SDK kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="4e35b-763">Ana bilgisayar sisteminde 64 bit çalışma zamanı bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="4e35b-764">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4e35b-765">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="4e35b-765">In-process hosting model</span></span>

<span data-ttu-id="4e35b-766">ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="4e35b-767">İşlem içi barındırma, işlem dışı barındırma üzerinde daha iyi performans sağlar çünkü:</span><span class="sxs-lookup"><span data-stu-id="4e35b-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="4e35b-768">İsteklerin geri döngü bağdaştırıcısı üzerinde proxy yok.</span><span class="sxs-lookup"><span data-stu-id="4e35b-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="4e35b-769">Geri döngü bağdaştırıcısı, giden ağ trafiğinin aynı makineye geri döndürdüğü bir ağ arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="4e35b-770">IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e35b-771">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="4e35b-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="4e35b-772">Uygulama başlatmayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-772">Performs app initialization.</span></span>
  * <span data-ttu-id="4e35b-773">[CoreCLR](/dotnet/standard/glossary#coreclr)'yi yükler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="4e35b-774">Çağırır `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="4e35b-775">IIS yerel isteğinin ömrünü işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="4e35b-776">İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="4e35b-777">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve süreçte barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![İşlem içi barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-inprocess.png)

<span data-ttu-id="4e35b-779">Web 'den çekirdek modu HTTP. sys sürücüsüne bir istek ulaşır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4e35b-780">Sürücü, yerel isteği Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4e35b-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4e35b-781">ASP.NET Core modülü yerel isteği alır ve IIS HTTP sunucusuna ( `IISHttpServer` ) geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="4e35b-782">IIS HTTP sunucusu, isteği yerelden yönetilene dönüştüren bir IIS için işlem içi sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="4e35b-783">IIS HTTP sunucusu isteği işlediğinde, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e35b-784">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e35b-785">Uygulamanın yanıtı IIS HTTP sunucusu aracılığıyla IIS 'e geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="4e35b-786">IIS yanıtı, isteği başlatan istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="4e35b-787">İşlem içi barındırma, mevcut uygulamalar için kabul ediyor, ancak tüm IIS ve IIS Express senaryoları için varsayılan [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar, işlem içi barındırma modeli için varsayılan olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="4e35b-788">`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.Server.IServer> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> [CoreCLR](/dotnet/standard/glossary#coreclr) 'YI önyüklemek ve uygulamayı IIS çalışan işleminin (*W3wp. exe* veya *iisexpress. exe*) içinde barındırmak için yöntemini çağırarak bir örnek ekler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="4e35b-789">Performans testleri, bir .NET Core uygulamasını işlem içinde barındıran uygulamanın, uygulamayı işlem dışı ve [Kestrel](xref:fundamentals/servers/kestrel) sunucusuna proxy alma isteklerinin barındırılmasına kıyasla önemli ölçüde daha yüksek istek aktarım hızı sağladığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4e35b-790">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="4e35b-790">Out-of-process hosting model</span></span>

<span data-ttu-id="4e35b-791">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, ASP.NET Core modülü işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="4e35b-792">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="4e35b-793">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e35b-794">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![İşlem dışı barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="4e35b-796">İstekler Web 'den çekirdek modu HTTP. sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4e35b-797">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4e35b-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4e35b-798">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="4e35b-799">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> uzantı sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="4e35b-800">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4e35b-801">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4e35b-802">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e35b-803">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e35b-804">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4e35b-805">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4e35b-806">ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4e35b-807">Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4e35b-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="4e35b-808">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="4e35b-809">Iısıntegration bileşenlerini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="4e35b-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="4e35b-810">`CreateWebHostBuilder`(*Program.cs*) içinde BIR konak oluştururken <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> IIS tümleştirmesini etkinleştirmek için çağırın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="4e35b-811">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/web-host#set-up-a-host> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="4e35b-812">IIS seçenekleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-812">IIS options</span></span>

<span data-ttu-id="4e35b-813">**İşlem içi barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="4e35b-813">**In-process hosting model**</span></span>

<span data-ttu-id="4e35b-814">IIS sunucu seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISServerOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="4e35b-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e35b-815">Aşağıdaki örnek, Automatıcauthentication 'ı devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="4e35b-816">Seçenek</span><span class="sxs-lookup"><span data-stu-id="4e35b-816">Option</span></span>                         | <span data-ttu-id="4e35b-817">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="4e35b-817">Default</span></span> | <span data-ttu-id="4e35b-818">Ayar</span><span class="sxs-lookup"><span data-stu-id="4e35b-818">Setting</span></span> |
| ---
<span data-ttu-id="4e35b-819">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-820">'Blazor'</span></span>
- <span data-ttu-id="4e35b-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-821">'Identity'</span></span>
- <span data-ttu-id="4e35b-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-823">'Razor'</span></span>
- <span data-ttu-id="4e35b-824">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-825">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-826">'Blazor'</span></span>
- <span data-ttu-id="4e35b-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-827">'Identity'</span></span>
- <span data-ttu-id="4e35b-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-829">'Razor'</span></span>
- <span data-ttu-id="4e35b-830">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-831">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-832">'Blazor'</span></span>
- <span data-ttu-id="4e35b-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-833">'Identity'</span></span>
- <span data-ttu-id="4e35b-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-835">'Razor'</span></span>
- <span data-ttu-id="4e35b-836">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-837">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-838">'Blazor'</span></span>
- <span data-ttu-id="4e35b-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-839">'Identity'</span></span>
- <span data-ttu-id="4e35b-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-841">'Razor'</span></span>
- <span data-ttu-id="4e35b-842">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-843">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-844">'Blazor'</span></span>
- <span data-ttu-id="4e35b-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-845">'Identity'</span></span>
- <span data-ttu-id="4e35b-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-847">'Razor'</span></span>
- <span data-ttu-id="4e35b-848">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-849">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-850">'Blazor'</span></span>
- <span data-ttu-id="4e35b-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-851">'Identity'</span></span>
- <span data-ttu-id="4e35b-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-853">'Razor'</span></span>
- <span data-ttu-id="4e35b-854">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-855">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-856">'Blazor'</span></span>
- <span data-ttu-id="4e35b-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-857">'Identity'</span></span>
- <span data-ttu-id="4e35b-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-859">'Razor'</span></span>
- <span data-ttu-id="4e35b-860">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-861">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-862">'Blazor'</span></span>
- <span data-ttu-id="4e35b-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-863">'Identity'</span></span>
- <span data-ttu-id="4e35b-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-865">'Razor'</span></span>
- <span data-ttu-id="4e35b-866">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-867">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-868">'Blazor'</span></span>
- <span data-ttu-id="4e35b-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-869">'Identity'</span></span>
- <span data-ttu-id="4e35b-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-871">'Razor'</span></span>
- <span data-ttu-id="4e35b-872">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-873">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-874">'Blazor'</span></span>
- <span data-ttu-id="4e35b-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-875">'Identity'</span></span>
- <span data-ttu-id="4e35b-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-877">'Razor'</span></span>
- <span data-ttu-id="4e35b-878">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-879">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-880">'Blazor'</span></span>
- <span data-ttu-id="4e35b-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-881">'Identity'</span></span>
- <span data-ttu-id="4e35b-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-883">'Razor'</span></span>
- <span data-ttu-id="4e35b-884">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-885">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-886">'Blazor'</span></span>
- <span data-ttu-id="4e35b-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-887">'Identity'</span></span>
- <span data-ttu-id="4e35b-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-889">'Razor'</span></span>
- <span data-ttu-id="4e35b-890">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-891">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-892">'Blazor'</span></span>
- <span data-ttu-id="4e35b-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-893">'Identity'</span></span>
- <span data-ttu-id="4e35b-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-895">'Razor'</span></span>
- <span data-ttu-id="4e35b-896">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-896">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-897">--------------- | :-----: | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-898">'Blazor'</span></span>
- <span data-ttu-id="4e35b-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-899">'Identity'</span></span>
- <span data-ttu-id="4e35b-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-901">'Razor'</span></span>
- <span data-ttu-id="4e35b-902">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-902">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-903">---- | | `AutomaticAuthentication`      | `true`  | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-904">İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e35b-905">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e35b-906">Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-907">| | `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="4e35b-908">**İşlem dışı barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="4e35b-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="4e35b-909">IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="4e35b-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e35b-910">Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="4e35b-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="4e35b-911">Seçenek</span><span class="sxs-lookup"><span data-stu-id="4e35b-911">Option</span></span>                         | <span data-ttu-id="4e35b-912">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="4e35b-912">Default</span></span> | <span data-ttu-id="4e35b-913">Ayar</span><span class="sxs-lookup"><span data-stu-id="4e35b-913">Setting</span></span> |
| ---
<span data-ttu-id="4e35b-914">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-915">'Blazor'</span></span>
- <span data-ttu-id="4e35b-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-916">'Identity'</span></span>
- <span data-ttu-id="4e35b-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-918">'Razor'</span></span>
- <span data-ttu-id="4e35b-919">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-920">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-921">'Blazor'</span></span>
- <span data-ttu-id="4e35b-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-922">'Identity'</span></span>
- <span data-ttu-id="4e35b-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-924">'Razor'</span></span>
- <span data-ttu-id="4e35b-925">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-926">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-927">'Blazor'</span></span>
- <span data-ttu-id="4e35b-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-928">'Identity'</span></span>
- <span data-ttu-id="4e35b-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-930">'Razor'</span></span>
- <span data-ttu-id="4e35b-931">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-932">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-933">'Blazor'</span></span>
- <span data-ttu-id="4e35b-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-934">'Identity'</span></span>
- <span data-ttu-id="4e35b-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-936">'Razor'</span></span>
- <span data-ttu-id="4e35b-937">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-938">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-939">'Blazor'</span></span>
- <span data-ttu-id="4e35b-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-940">'Identity'</span></span>
- <span data-ttu-id="4e35b-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-942">'Razor'</span></span>
- <span data-ttu-id="4e35b-943">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-944">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-945">'Blazor'</span></span>
- <span data-ttu-id="4e35b-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-946">'Identity'</span></span>
- <span data-ttu-id="4e35b-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-948">'Razor'</span></span>
- <span data-ttu-id="4e35b-949">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-950">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-951">'Blazor'</span></span>
- <span data-ttu-id="4e35b-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-952">'Identity'</span></span>
- <span data-ttu-id="4e35b-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-954">'Razor'</span></span>
- <span data-ttu-id="4e35b-955">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-956">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-957">'Blazor'</span></span>
- <span data-ttu-id="4e35b-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-958">'Identity'</span></span>
- <span data-ttu-id="4e35b-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-960">'Razor'</span></span>
- <span data-ttu-id="4e35b-961">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-962">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-963">'Blazor'</span></span>
- <span data-ttu-id="4e35b-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-964">'Identity'</span></span>
- <span data-ttu-id="4e35b-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-966">'Razor'</span></span>
- <span data-ttu-id="4e35b-967">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-968">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-969">'Blazor'</span></span>
- <span data-ttu-id="4e35b-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-970">'Identity'</span></span>
- <span data-ttu-id="4e35b-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-972">'Razor'</span></span>
- <span data-ttu-id="4e35b-973">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-974">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-975">'Blazor'</span></span>
- <span data-ttu-id="4e35b-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-976">'Identity'</span></span>
- <span data-ttu-id="4e35b-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-978">'Razor'</span></span>
- <span data-ttu-id="4e35b-979">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-980">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-981">'Blazor'</span></span>
- <span data-ttu-id="4e35b-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-982">'Identity'</span></span>
- <span data-ttu-id="4e35b-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-984">'Razor'</span></span>
- <span data-ttu-id="4e35b-985">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-986">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-987">'Blazor'</span></span>
- <span data-ttu-id="4e35b-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-988">'Identity'</span></span>
- <span data-ttu-id="4e35b-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-990">'Razor'</span></span>
- <span data-ttu-id="4e35b-991">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-991">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-992">--------------- | :-----: | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-993">'Blazor'</span></span>
- <span data-ttu-id="4e35b-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-994">'Identity'</span></span>
- <span data-ttu-id="4e35b-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-996">'Razor'</span></span>
- <span data-ttu-id="4e35b-997">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-997">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-998">---- | | `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-999">İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e35b-1000">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e35b-1001">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="4e35b-1002">| | `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="4e35b-1003">| | `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4e35b-1004">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4e35b-1005">Iletilen üstbilgiler ara yazılımını yapılandıran [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components)ve ASP.NET Core modülü, DÜZENI (http/https) ve isteğin KAYNAKLANDıĞı uzak IP adresini iletecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="4e35b-1006">Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="4e35b-1007">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="4e35b-1008">Web. config dosyası</span><span class="sxs-lookup"><span data-stu-id="4e35b-1008">web.config file</span></span>

<span data-ttu-id="4e35b-1009">*Web. config* dosyası [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e35b-1010">*Web. config* dosyası oluşturma, dönüştürme ve yayımlama, proje yayımlandığında bir MSBuild hedefi () tarafından işlenir `_TransformWebConfig` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="4e35b-1011">Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="4e35b-1012">SDK proje dosyasının en üstünde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4e35b-1013">Bir *Web. config* dosyası projede yoksa, dosya ASP.NET Core modülünü yapılandırmak Için doğru *processPath* ve *bağımsız değişkenlerle* oluşturulur ve [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="4e35b-1014">Projede bir *Web. config* dosyası varsa, dosya ASP.NET Core modülünü yapılandırmak ve yayımlanan çıktıya taşınmak Için doğru *processPath* ve *bağımsız değişkenlerle* birlikte dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="4e35b-1015">Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="4e35b-1016">*Web. config* dosyası, etkin IIS modüllerini DENETLEYEN ek IIS yapılandırma ayarları verebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="4e35b-1017">ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="4e35b-1018">Web SDK 'sının *Web. config* dosyasını dönüştürülmesini engellemek için, **\<IsTransformWebConfigDisabled>** Proje dosyasındaki özelliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="4e35b-1019">Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakıldığında, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="4e35b-1020">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="4e35b-1021">Web. config dosyası konumu</span><span class="sxs-lookup"><span data-stu-id="4e35b-1021">web.config file location</span></span>

<span data-ttu-id="4e35b-1022">[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, *Web. config* dosyası dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="4e35b-1023">Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="4e35b-1024">Web Dağıtımı kullanarak birden çok uygulama yayımlamayı etkinleştirmek için uygulamanın kökünde *Web. config* dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="4e35b-1025">Uygulamanın fiziksel yolunda \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . xml* (XML belge açıklamaları) ve \* \<assembly> . Deps. JSON\*gibi hassas dosyalar bulunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="4e35b-1026">*Web. config* dosyası mevcut olduğunda ve site normal olarak başlatıldığında, istenirse IIS bu hassas dosyaları sunmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="4e35b-1027">*Web. config* dosyası eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="4e35b-1028">***Web. config* dosyasının her zaman dağıtımda mevcut olması, doğru şekilde adlandırılması ve siteyi normal başlangıç için yapılandırabiliyor olması gerekir. *Web. config* dosyasını bir üretim dağıtımından hiçbir şekilde kaldırmayın.**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="4e35b-1029">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="4e35b-1029">Transform web.config</span></span>

<span data-ttu-id="4e35b-1030">*Web. config* 'i yayımlama sırasında dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlayın), bkz <xref:host-and-deploy/iis/transform-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="4e35b-1031">IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-1031">IIS configuration</span></span>

<span data-ttu-id="4e35b-1032">**Windows Server işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="4e35b-1033">**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="4e35b-1034">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi**bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="4e35b-1035">**Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="4e35b-1037">**Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="4e35b-1038">İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="4e35b-1040">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1041">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="4e35b-1042">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e35b-1043">Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e35b-1044">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1045">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e35b-1046">WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="4e35b-1047">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e35b-1048">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e35b-1049">Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="4e35b-1050">**Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="4e35b-1051">**Windows masaüstü işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="4e35b-1052">**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini**etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e35b-1053">**Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="4e35b-1054">**Internet Information Services** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="4e35b-1055">**Web yönetimi araçları** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="4e35b-1056">**IIS Yönetim Konsolu**kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="4e35b-1057">**World Wide Web Hizmetleri**kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e35b-1058">**World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="4e35b-1059">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1060">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="4e35b-1061">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e35b-1062">Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e35b-1063">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1064">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e35b-1065">WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="4e35b-1066">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e35b-1067">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e35b-1068">IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1068">If the IIS installation requires a restart, restart the system.</span></span>

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="4e35b-1070">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4e35b-1071">*.NET Core barındırma paketi* 'ni barındırma sistemine yükler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="4e35b-1072">Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e35b-1073">Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e35b-1074">Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="4e35b-1075">IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="4e35b-1076">.NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4e35b-1077">Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="4e35b-1078">İndir</span><span class="sxs-lookup"><span data-stu-id="4e35b-1078">Download</span></span>

1. <span data-ttu-id="4e35b-1079">[.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="4e35b-1080">İstediğiniz .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="4e35b-1081">**Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="4e35b-1082">**Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="4e35b-1083">Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="4e35b-1084">Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="4e35b-1085">Barındırma paketini yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="4e35b-1086">Yükleyiciyi sunucuda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1086">Run the installer on the server.</span></span> <span data-ttu-id="4e35b-1087">Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="4e35b-1088">`OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="4e35b-1089">`OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="4e35b-1090">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e35b-1091">`OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="4e35b-1092">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e35b-1093">`OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="4e35b-1094">32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="4e35b-1095">Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="4e35b-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma (*ApplicationHost. config*) IIS yüklemesiyle aynı makinada olduğunda, IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="4e35b-1097">*Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="4e35b-1098">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="4e35b-1099">Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="4e35b-1100">IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="4e35b-1101">Barındırma paketini yüklerken IIS 'deki bireysel siteleri el ile durdurmanız gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="4e35b-1102">Barındırılan uygulamalar (IIS siteleri) IIS yeniden başlatıldığında yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="4e35b-1103">Uygulamalar, [uygulama başlatma modülünden](#application-initialization-module-and-idle-timeout)da dahil olmak üzere ilk isteklerini alırken yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="4e35b-1104">ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için ileri sarma davranışını benimseme.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="4e35b-1105">IIS ile IIS tarafından barındırılan uygulamalar IIS ile yeniden başlatıldığında, ilk isteklerini alırken başvurulan paketlerinin en son düzeltme eki sürümleriyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="4e35b-1106">IIS yeniden başlatılırsa, uygulamalar yeniden başlatılır ve çalışan işlemlerine geri dönüştürüldüğünde geri alma davranışını gösterir ve ilk isteklerini alırlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="4e35b-1107">IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="4e35b-1108">Visual Studio ile yayımlarken Web Dağıtımı yüklemesi</span><span class="sxs-lookup"><span data-stu-id="4e35b-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="4e35b-1109">Uygulamaları [Web dağıtımı](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)olan sunuculara dağıttığınızda, en son Web dağıtımı sürümünü sunucuya yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="4e35b-1110">Web Dağıtımı yüklemek için [Web Platformu Yükleyicisi 'ni (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft İndirme Merkezi](https://www.microsoft.com/download/details.aspx?id=43717)'nden bir yükleyici edinin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="4e35b-1111">Tercih edilen yöntem, WebPI kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="4e35b-1112">WebPI, barındırma sağlayıcıları için tek başına kurulum ve yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="4e35b-1113">IIS sitesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1113">Create the IIS site</span></span>

1. <span data-ttu-id="4e35b-1114">Barındırma sisteminde, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="4e35b-1115">Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="4e35b-1116">Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="4e35b-1117">IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="4e35b-1118">**Siteler** klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="4e35b-1119">Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="4e35b-1120">Bir **site adı** belirtin ve **fiziksel yolu** uygulamanın dağıtım klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="4e35b-1121">**Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Web sitesi Ekle adımında site adı, fiziksel yol ve ana bilgisayar adını sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="4e35b-1123">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4e35b-1124">Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="4e35b-1125">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4e35b-1126">Joker karakterler yerine açık ana bilgisayar adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="4e35b-1127">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4e35b-1128">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="4e35b-1129">Sunucu düğümünün altında **uygulama havuzları**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="4e35b-1130">Sitenin uygulama havuzuna sağ tıklayın ve bağlam menüsünden **temel ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="4e35b-1131">**Uygulama havuzunu Düzenle** penceresinde, **.NET CLR sürümünü** **yönetilen kod olmadan**ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR sürümü için yönetilen kod ayarlama.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="4e35b-1133">ASP.NET Core ayrı bir işlemde çalışır ve çalışma zamanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="4e35b-1134">ASP.NET Core, masaüstü CLR 'nin (.NET CLR) yüklenmemesine bağlı değildir &mdash; . .NET Core Için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı çalışan işlemde barındırmak için önyüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="4e35b-1135">**.NET CLR sürümünün** **yönetilen kod olmadan** ayarlanması isteğe bağlıdır, ancak önerilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="4e35b-1136">*ASP.NET Core 2,2 veya üzeri*: [işlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [tabanlı bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, 32-bit (x86) işlemleri için uygulama havuzunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="4e35b-1137">IIS Yöneticisi > **uygulama havuzlarının** **Eylemler** kenar çubuğunda, **uygulama havuzu varsayılanlarını** veya **Gelişmiş ayarları**ayarla ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="4e35b-1138">**32 bitlik uygulamaları etkinleştir** ' i bulun ve değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="4e35b-1139">Bu ayar [, işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="4e35b-1140">İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="4e35b-1141">Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) **applicationpokaydentity** 'den başka bir kimliğe değiştiyse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="4e35b-1142">Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="4e35b-1143">**Windows kimlik doğrulama yapılandırması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="4e35b-1144">Daha fazla bilgi için bkz. [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="4e35b-1145">Uygulamayı dağıtma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1145">Deploy the app</span></span>

<span data-ttu-id="4e35b-1146">Uygulamayı [IIS sitesi oluşturma](#create-the-iis-site) bölümünde oluşturulan IIS **fiziksel yol** klasörüne dağıtın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="4e35b-1147">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *Publish* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="4e35b-1148">Visual Studio ile Web Dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="4e35b-1149">Web Dağıtımı kullanılacak bir yayımlama profili oluşturma hakkında bilgi edinmek için bkz. [ASP.NET Core App Deployment Için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="4e35b-1150">Barındırma sağlayıcısı, bir yayımlama profili veya oluşturma desteği sağlıyorsa, profilini indirin ve Visual Studio **Yayımlama** iletişim kutusunu kullanarak içeri aktarın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Yayımla iletişim sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="4e35b-1152">Visual Studio dışında Web Dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="4e35b-1153">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) , komut satırından Visual Studio dışında da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="4e35b-1154">Daha fazla bilgi için bkz. [Web Dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="4e35b-1155">Web Dağıtımı alternatifleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="4e35b-1156">Uygulamayı barındırma sistemine taşımak için el ile kopyalama, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi çeşitli yöntemlerden birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="4e35b-1157">IIS 'ye dağıtım ASP.NET Core hakkında daha fazla bilgi için, [IIS yöneticileri Için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="4e35b-1158">Web sitesine gidin</span><span class="sxs-lookup"><span data-stu-id="4e35b-1158">Browse the website</span></span>

<span data-ttu-id="4e35b-1159">Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın genel uç noktalarından birine bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="4e35b-1160">Aşağıdaki örnekte, site bağlantı noktasındaki IIS **ana bilgisayar adına** bağlıdır `www.mysite.com` **Port** `80` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="4e35b-1161">Bir istek şu şekilde yapılır `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="4e35b-1161">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge tarayıcısı, IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="4e35b-1163">Kilitli dağıtım dosyaları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1163">Locked deployment files</span></span>

<span data-ttu-id="4e35b-1164">Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="4e35b-1165">Dağıtım sırasında kilitli dosyaların üzerine yazılamaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="4e35b-1166">Kilitli dosyaları bir dağıtımda serbest bırakmak için aşağıdaki yaklaşımlardan **birini** kullanarak uygulama havuzunu durdurun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="4e35b-1167">Proje dosyasında Web Dağıtımı ve başvurusunu kullanın `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="4e35b-1168">Bir *app_offline. htm* dosyası Web uygulaması dizininin köküne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="4e35b-1169">Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="4e35b-1170">Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="4e35b-1171">Sunucu üzerindeki IIS Yöneticisi 'nde uygulama havuzunu el ile durdurun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="4e35b-1172">*App_offline. htm* dosyasını bırakmak için PowerShell kullanın (PowerShell 5 veya üzerini gerektirir):</span><span class="sxs-lookup"><span data-stu-id="4e35b-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="4e35b-1173">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1173">Data protection</span></span>

<span data-ttu-id="4e35b-1174">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="4e35b-1175">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="4e35b-1176">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="4e35b-1177">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="4e35b-1178">Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçleri geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="4e35b-1179">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="4e35b-1180">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="4e35b-1181">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata)içerebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="4e35b-1182">Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="4e35b-1183">**Veri koruma kayıt defteri anahtarları oluşturma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="4e35b-1184">ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="4e35b-1185">Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="4e35b-1186">Tek başına, Web grubu olmayan IIS yüklemeleri için [Data Protection provision-AutoGenKeys. ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="4e35b-1187">Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="4e35b-1188">Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="4e35b-1189">Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="4e35b-1190">Varsayılan olarak, veri koruma anahtarları şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="4e35b-1191">Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştırıldığı Windows hesabıyla sınırlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="4e35b-1192">Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="4e35b-1193">Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün: sertifikaları kullanıcının güvenilen sertifika deposuna yerleştir ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="4e35b-1194">Ayrıntılar için bkz. [ASP.NET Core veri korumasını yapılandırma](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="4e35b-1195">**Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="4e35b-1196">Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="4e35b-1197">**Kullanıcı profilini yükle** ' ye ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="4e35b-1198">Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="4e35b-1199">Anahtarlar *% LocalAppData%/ASP.exe. net/DataProtection-Keys* klasörüne kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="4e35b-1200">Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="4e35b-1201">Varsayılan değeri `setProfileEnvironment` `true` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="4e35b-1202">Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="4e35b-1203">Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="4e35b-1204">*% Windir%/system32/inetsrv/config* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="4e35b-1205">*ApplicationHost. config* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="4e35b-1206">Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="4e35b-1207">`setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="4e35b-1208">**Dosya sistemini anahtar halka deposu olarak kullanma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="4e35b-1209">[Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="4e35b-1210">Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="4e35b-1211">Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="4e35b-1212">IIS 'yi bir Web grubunda kullanırken:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="4e35b-1213">Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="4e35b-1214">Her makineye bir x509 sertifikası dağıtın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="4e35b-1215">[Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="4e35b-1216">**Veri koruma için makineye özel bir ilke ayarlama**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="4e35b-1217">Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="4e35b-1218">Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="4e35b-1219">Sanal Dizinler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1219">Virtual Directories</span></span>

<span data-ttu-id="4e35b-1220">[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="4e35b-1221">Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="4e35b-1222">Alt uygulamalar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1222">Sub-applications</span></span>

<span data-ttu-id="4e35b-1223">ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="4e35b-1224">Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="4e35b-1225">Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="4e35b-1226">Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="4e35b-1227">Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="4e35b-1228">Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="4e35b-1229">İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="4e35b-1230">Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="4e35b-1231">Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="4e35b-1232">Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="4e35b-1233">Alt uygulama için bir uygulama havuzu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="4e35b-1234">.NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="4e35b-1235">Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="4e35b-1236">IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="4e35b-1237">**Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="4e35b-1238">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1238">Select **OK**.</span></span>

<span data-ttu-id="4e35b-1239">Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="4e35b-1240">İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="4e35b-1241">Web. config ile IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="4e35b-1242">IIS yapılandırması, `<system.webServer>` ASP.NET Core modüllü ASP.NET Core uygulamalar için işlevsel olan IIS senaryoları için *Web. config* 'in bölümü tarafından etkilenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="4e35b-1243">Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="4e35b-1244">IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulamanın *Web. config* dosyasındaki öğesi ASP.NET Core bir uygulama için devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="4e35b-1245">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="4e35b-1246">İçin yapılandırma başvurusu\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="4e35b-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="4e35b-1247">Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri \<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *Appcmd. exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="4e35b-1248">Web. config 'in yapılandırma bölümleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="4e35b-1249">*Web. config* dosyasındaki ASP.NET 4. x uygulamalarının yapılandırma bölümleri yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmaz:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="4e35b-1250">ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="4e35b-1251">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="4e35b-1252">Uygulama havuzları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1252">Application Pools</span></span>

<span data-ttu-id="4e35b-1253">Uygulama havuzu yalıtımı, barındırma modeliyle belirlenir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="4e35b-1254">İşlem içi barındırma: uygulamaların ayrı uygulama havuzlarında çalışması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="4e35b-1255">İşlem dışı barındırma: her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="4e35b-1256">IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzu olur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="4e35b-1257">Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="4e35b-1258">Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="4e35b-1259">Uygulama havuzuIdentity</span><span class="sxs-lookup"><span data-stu-id="4e35b-1259">Application Pool Identity</span></span>

<span data-ttu-id="4e35b-1260">Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="4e35b-1261">IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="4e35b-1262">Uygulama havuzunun **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu 'nda, uygulamasının **Identity** **applicationpokaydentity**kullanacak şekilde ayarlandığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

<span data-ttu-id="4e35b-1264">IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="4e35b-1265">Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="4e35b-1266">Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="4e35b-1267">IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="4e35b-1268">Windows Gezgini 'ni açın ve dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="4e35b-1269">Dizine sağ tıklayıp **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="4e35b-1270">**Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="4e35b-1271">**Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="4e35b-1272">**Seçilecek nesne adlarını girin** alanına \*\* \\ app_pool_name>IIS AppPool<\*\* girin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="4e35b-1273">**Adları denetle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="4e35b-1274">*DefaultAppPool* için **IIS APPPOOL\DefaultAppPool**kullanarak adları denetleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="4e35b-1275">**Adları denetle** düğmesi seçildiğinde, nesne adları alanında bir **DefaultAppPool** değeri gösterilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="4e35b-1276">Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="4e35b-1277">Nesne adı denetlenirken **IIS AppPool \\<APP_POOL_NAME>** biçimini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="4e35b-1279">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1279">Select **OK**.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="4e35b-1281">Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="4e35b-1282">Gerektiğinde ek izinler sağlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="4e35b-1283">Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="4e35b-1284">Örnek olarak *DefaultAppPool* kullanarak, aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="4e35b-1285">Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4e35b-1286">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="4e35b-1286">HTTP/2 support</span></span>

<span data-ttu-id="4e35b-1287">[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="4e35b-1288">İşlem içi</span><span class="sxs-lookup"><span data-stu-id="4e35b-1288">In-process</span></span>
  * <span data-ttu-id="4e35b-1289">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="4e35b-1290">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="4e35b-1291">İşlem dışı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1291">Out-of-process</span></span>
  * <span data-ttu-id="4e35b-1292">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="4e35b-1293">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="4e35b-1294">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4e35b-1295">Bir HTTP/2 bağlantısı oluşturulduğunda, bir işlem içi dağıtım için, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="4e35b-1296">Bir HTTP/2 bağlantısı oluşturulduğunda bir işlem dışı dağıtımı için, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="4e35b-1297">İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4e35b-1298">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4e35b-1299">HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="4e35b-1300">IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="4e35b-1301">CORS ön denetim istekleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1301">CORS preflight requests</span></span>

<span data-ttu-id="4e35b-1302">*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="4e35b-1303">.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="4e35b-1304">*Web. config* DOSYASıNDAKI uygulama IIS işleyicilerini seçenek isteklerini geçecek şekilde yapılandırma hakkında bilgi edinmek için bkz. [ASP.NET Web API 2 ' de çapraz kaynak ISTEKLERINI etkinleştirme: CORS 'nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="4e35b-1305">Uygulama başlatma modülü ve boşta kalma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="4e35b-1306">ASP.NET Core modülü sürüm 2 tarafından IIS 'de barındırıldığında:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="4e35b-1307">[Uygulama başlatma modülü](#application-initialization-module): uygulamanın çalışan işlem yeniden başlatması veya sunucu yeniden başlatması üzerinde otomatik olarak başlayacak şekilde, [uygulamanın barındırılan veya](#in-process-hosting-model) [işlem dışı](#out-of-process-hosting-model) bir şekilde yapılandırılmış olması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="4e35b-1308">[Boşta kalma zaman aşımı](#idle-timeout): uygulamanın şirket [içinde barındırılan işlemi, işlem](#in-process-hosting-model) yapılmayan dönemler sırasında zaman aşımına uğramaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="4e35b-1309">Uygulama başlatma modülü</span><span class="sxs-lookup"><span data-stu-id="4e35b-1309">Application Initialization Module</span></span>

<span data-ttu-id="4e35b-1310">*İşlem içi ve işlem dışı barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="4e35b-1311">[IIS uygulaması başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , uygulama havuzu başlatıldığında veya geri DÖNÜŞTÜRÜLDÜĞÜNDE uygulamaya http isteği gönderen bir IIS özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="4e35b-1312">İstek, uygulamayı başlatmak üzere tetikler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1312">The request triggers the app to start.</span></span> <span data-ttu-id="4e35b-1313">Varsayılan olarak IIS, uygulamayı başlatmak için uygulamanın kök URL 'SI () için bir istek yayınlar `/` (yapılandırma hakkında daha fazla bilgi için [ek kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="4e35b-1314">IIS uygulama başlatma rolü özelliğinin etkin olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="4e35b-1315">IIS 'yi yerel olarak kullanırken Windows 7 veya üzeri masaüstü sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="4e35b-1316">**Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="4e35b-1317">**Internet Information Services** > **World Wide Web Services** > **uygulama geliştirme özelliklerini**açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="4e35b-1318">**Uygulama başlatma**onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="4e35b-1319">Windows Server 2008 R2 veya sonraki sürümlerde:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="4e35b-1320">**Rol ve Özellik Ekleme Sihirbazı 'nı**açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="4e35b-1321">**Rol hizmetlerini Seç** panelinde, **uygulama geliştirme** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="4e35b-1322">**Uygulama başlatma**onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="4e35b-1323">Site için uygulama başlatma modülünü etkinleştirmek üzere aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="4e35b-1324">IIS Yöneticisi 'Ni kullanma:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="4e35b-1325">**Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="4e35b-1326">Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="4e35b-1327">Varsayılan **Başlangıç modu** **OnDemand**' dir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="4e35b-1328">**Başlangıç modunu** **AlwaysRunning**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="4e35b-1329">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="4e35b-1330">**Bağlantılar** panelinde **siteler** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="4e35b-1331">Uygulamaya sağ tıklayın ve **Web sitesi** > **Gelişmiş ayarlarını**Yönet ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="4e35b-1332">Varsayılan **önyükleme etkin** ayarı **false**şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="4e35b-1333">**Önyükleme etkin** ' i **true**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="4e35b-1334">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1334">Select **OK**.</span></span>

* <span data-ttu-id="4e35b-1335">*Web. config*kullanarak `<applicationInitialization>` öğesini, `doAppInitAfterRestart` `true` `<system.webServer>` uygulamasının *Web. config* dosyasındaki öğelerine ayarlı öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="4e35b-1336">Boşta Kalma Süresi Zaman Aşımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1336">Idle Timeout</span></span>

<span data-ttu-id="4e35b-1337">*Yalnızca işlem sırasında barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="4e35b-1338">Uygulamanın çalışmasını engellemek için, IIS Yöneticisi 'Ni kullanarak uygulama havuzunun boşta kalma zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="4e35b-1339">**Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="4e35b-1340">Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="4e35b-1341">Varsayılan **boşta kalma süresi (dakika)** **20** dakikadır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="4e35b-1342">**Boşta kalma zaman aşımını (dakika)** **0** (sıfır) olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="4e35b-1343">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1343">Select **OK**.</span></span>
1. <span data-ttu-id="4e35b-1344">Çalışan işlemini geri dönüştür.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1344">Recycle the worker process.</span></span>

<span data-ttu-id="4e35b-1345">[İşlem dışı](#out-of-process-hosting-model) barındırılan uygulamaların zaman aşımına uğramasını engellemek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="4e35b-1346">Uygulamayı çalışır durumda tutmak için bir dış hizmetten ping yapın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="4e35b-1347">Uygulama yalnızca arka plan hizmetleri barındırıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için bir Windows hizmeti](xref:host-and-deploy/windows-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="4e35b-1348">Uygulama başlatma modülü ve boşta kalma zaman aşımı ek kaynakları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="4e35b-1349">IIS 8,0 uygulama başlatma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="4e35b-1350">[Uygulama başlatma \<applicationInitialization> ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="4e35b-1351">[Uygulama havuzu \<processModel> Için Işlem modeli ayarları ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="4e35b-1352">IIS yöneticileri için dağıtım kaynakları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="4e35b-1353">IIS belgeleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="4e35b-1354">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="4e35b-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="4e35b-1355">.NET Core uygulama dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="4e35b-1356">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="4e35b-1357">Resmi Microsoft IIS sitesi</span><span class="sxs-lookup"><span data-stu-id="4e35b-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="4e35b-1358">Windows Server teknik içerik kitaplığı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="4e35b-1359">IIS üzerinde HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4e35b-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4e35b-1360">ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="4e35b-1361">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="4e35b-1362">Desteklenen işletim sistemleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1362">Supported operating systems</span></span>

<span data-ttu-id="4e35b-1363">Aşağıdaki işletim sistemleri desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="4e35b-1364">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1364">Windows 7 or later</span></span>
* <span data-ttu-id="4e35b-1365">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="4e35b-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4e35b-1366">[Http. sys sunucusu](xref:fundamentals/servers/httpsys) (eskiden webListener olarak adlandırılır), IIS ile ters proxy yapılandırmasında çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="4e35b-1367">[Kestrel sunucusunu](xref:fundamentals/servers/kestrel)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4e35b-1368">Azure 'da barındırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/azure-apps/index> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="4e35b-1369">Sorun giderme kılavuzu için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="4e35b-1370">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1370">Supported platforms</span></span>

<span data-ttu-id="4e35b-1371">32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="4e35b-1372">Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="4e35b-1373">64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="4e35b-1374">Daha büyük IIS yığın boyutunu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="4e35b-1375">64 bitlik yerel bağımlılıklara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="4e35b-1376">64 bit uygulama yayımlamak için 64 bit (x64) .NET Core SDK kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="4e35b-1377">Ana bilgisayar sisteminde 64 bit çalışma zamanı bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="4e35b-1378">ASP.NET Core, varsayılan, platformlar arası bir HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="4e35b-1379">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama IIS çalışan işleminden (*işlem dışı*) [Kestrel sunucusu](xref:fundamentals/servers/index#kestrel)ile ayrı bir işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="4e35b-1380">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="4e35b-1381">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="4e35b-1382">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e35b-1383">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core Modülü](index/_static/ancm-outofprocess.png)

<span data-ttu-id="4e35b-1385">İstekler Web 'den çekirdek modu HTTP. sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4e35b-1386">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4e35b-1387">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="4e35b-1388">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="4e35b-1389">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4e35b-1390">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4e35b-1391">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e35b-1392">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e35b-1393">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4e35b-1394">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4e35b-1395">`CreateDefaultBuilder`Web sunucusu olarak [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu yapılandırır ve [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)için temel yolu ve bağlantı noktasını yapılandırarak IIS tümleştirmesini sunar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="4e35b-1396">ASP.NET Core modülü, arka uç işlemine atanacak dinamik bir bağlantı noktası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="4e35b-1397">`CreateDefaultBuilder`yöntemini çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="4e35b-1398">`UseIISIntegration`Kestrel 'i, localhost IP adresinde () dinamik bağlantı noktasında dinlemek üzere yapılandırır `127.0.0.1` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="4e35b-1399">Dinamik bağlantı noktası 1234 ise, Kestrel dinler `127.0.0.1:1234` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="4e35b-1400">Bu yapılandırma tarafından belirtilen diğer URL yapılandırmalarının yerini alır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="4e35b-1401">Kestrel 'in dinleme API 'SI</span><span class="sxs-lookup"><span data-stu-id="4e35b-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="4e35b-1402">[Yapılandırma](xref:fundamentals/configuration/index) (veya [komut satırı--URL seçeneği](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="4e35b-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="4e35b-1403">`UseUrls`Modül kullanılırken, veya Kestrel API 'sine yapılan çağrılar `Listen` gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="4e35b-1404">`UseUrls`Veya `Listen` çağrılırsa, Kestrel yalnızca uygulamayı IIS olmadan çalıştırırken belirtilen bağlantı noktasını dinler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="4e35b-1405">ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4e35b-1406">Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="4e35b-1407">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="4e35b-1408">Iısıntegration bileşenlerini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="4e35b-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="4e35b-1409">`CreateWebHostBuilder`(*Program.cs*) içinde BIR konak oluştururken <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> IIS tümleştirmesini etkinleştirmek için çağırın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="4e35b-1410">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/web-host#set-up-a-host> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="4e35b-1411">IIS seçenekleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1411">IIS options</span></span>

| <span data-ttu-id="4e35b-1412">Seçenek</span><span class="sxs-lookup"><span data-stu-id="4e35b-1412">Option</span></span>                         | <span data-ttu-id="4e35b-1413">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="4e35b-1413">Default</span></span> | <span data-ttu-id="4e35b-1414">Ayar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1414">Setting</span></span> |
| ---
<span data-ttu-id="4e35b-1415">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1416">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1417">'Identity'</span></span>
- <span data-ttu-id="4e35b-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1419">'Razor'</span></span>
- <span data-ttu-id="4e35b-1420">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1421">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1422">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1423">'Identity'</span></span>
- <span data-ttu-id="4e35b-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1425">'Razor'</span></span>
- <span data-ttu-id="4e35b-1426">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1427">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1428">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1429">'Identity'</span></span>
- <span data-ttu-id="4e35b-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1431">'Razor'</span></span>
- <span data-ttu-id="4e35b-1432">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1433">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1434">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1435">'Identity'</span></span>
- <span data-ttu-id="4e35b-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1437">'Razor'</span></span>
- <span data-ttu-id="4e35b-1438">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1439">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1440">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1441">'Identity'</span></span>
- <span data-ttu-id="4e35b-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1443">'Razor'</span></span>
- <span data-ttu-id="4e35b-1444">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1445">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1446">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1447">'Identity'</span></span>
- <span data-ttu-id="4e35b-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1449">'Razor'</span></span>
- <span data-ttu-id="4e35b-1450">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1451">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1452">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1453">'Identity'</span></span>
- <span data-ttu-id="4e35b-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1455">'Razor'</span></span>
- <span data-ttu-id="4e35b-1456">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1457">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1458">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1459">'Identity'</span></span>
- <span data-ttu-id="4e35b-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1461">'Razor'</span></span>
- <span data-ttu-id="4e35b-1462">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1463">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1464">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1465">'Identity'</span></span>
- <span data-ttu-id="4e35b-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1467">'Razor'</span></span>
- <span data-ttu-id="4e35b-1468">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1469">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1470">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1471">'Identity'</span></span>
- <span data-ttu-id="4e35b-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1473">'Razor'</span></span>
- <span data-ttu-id="4e35b-1474">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1475">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1476">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1477">'Identity'</span></span>
- <span data-ttu-id="4e35b-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1479">'Razor'</span></span>
- <span data-ttu-id="4e35b-1480">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1481">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1482">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1483">'Identity'</span></span>
- <span data-ttu-id="4e35b-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1485">'Razor'</span></span>
- <span data-ttu-id="4e35b-1486">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1487">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1488">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1489">'Identity'</span></span>
- <span data-ttu-id="4e35b-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1491">'Razor'</span></span>
- <span data-ttu-id="4e35b-1492">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-1493">--------------- | :-----: | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1494">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1495">'Identity'</span></span>
- <span data-ttu-id="4e35b-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1497">'Razor'</span></span>
- <span data-ttu-id="4e35b-1498">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-1499">---- | | `AutomaticAuthentication`      | `true`  | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-1500">İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e35b-1501">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e35b-1502">Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-1503">| | `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="4e35b-1504">IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e35b-1505">Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="4e35b-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="4e35b-1506">Seçenek</span><span class="sxs-lookup"><span data-stu-id="4e35b-1506">Option</span></span>                         | <span data-ttu-id="4e35b-1507">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="4e35b-1507">Default</span></span> | <span data-ttu-id="4e35b-1508">Ayar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1508">Setting</span></span> |
| ---
<span data-ttu-id="4e35b-1509">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1510">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1511">'Identity'</span></span>
- <span data-ttu-id="4e35b-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1513">'Razor'</span></span>
- <span data-ttu-id="4e35b-1514">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1515">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1516">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1517">'Identity'</span></span>
- <span data-ttu-id="4e35b-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1519">'Razor'</span></span>
- <span data-ttu-id="4e35b-1520">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1521">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1522">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1523">'Identity'</span></span>
- <span data-ttu-id="4e35b-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1525">'Razor'</span></span>
- <span data-ttu-id="4e35b-1526">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1527">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1528">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1529">'Identity'</span></span>
- <span data-ttu-id="4e35b-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1531">'Razor'</span></span>
- <span data-ttu-id="4e35b-1532">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1533">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1534">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1535">'Identity'</span></span>
- <span data-ttu-id="4e35b-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1537">'Razor'</span></span>
- <span data-ttu-id="4e35b-1538">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1539">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1540">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1541">'Identity'</span></span>
- <span data-ttu-id="4e35b-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1543">'Razor'</span></span>
- <span data-ttu-id="4e35b-1544">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1545">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1546">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1547">'Identity'</span></span>
- <span data-ttu-id="4e35b-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1549">'Razor'</span></span>
- <span data-ttu-id="4e35b-1550">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1551">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1552">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1553">'Identity'</span></span>
- <span data-ttu-id="4e35b-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1555">'Razor'</span></span>
- <span data-ttu-id="4e35b-1556">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1557">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1558">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1559">'Identity'</span></span>
- <span data-ttu-id="4e35b-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1561">'Razor'</span></span>
- <span data-ttu-id="4e35b-1562">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1563">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1564">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1565">'Identity'</span></span>
- <span data-ttu-id="4e35b-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1567">'Razor'</span></span>
- <span data-ttu-id="4e35b-1568">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1569">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1570">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1571">'Identity'</span></span>
- <span data-ttu-id="4e35b-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1573">'Razor'</span></span>
- <span data-ttu-id="4e35b-1574">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1575">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1576">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1577">'Identity'</span></span>
- <span data-ttu-id="4e35b-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1579">'Razor'</span></span>
- <span data-ttu-id="4e35b-1580">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4e35b-1581">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1582">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1583">'Identity'</span></span>
- <span data-ttu-id="4e35b-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1585">'Razor'</span></span>
- <span data-ttu-id="4e35b-1586">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-1587">--------------- | :-----: | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4e35b-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1588">'Blazor'</span></span>
- <span data-ttu-id="4e35b-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1589">'Identity'</span></span>
- <span data-ttu-id="4e35b-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="4e35b-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4e35b-1591">'Razor'</span></span>
- <span data-ttu-id="4e35b-1592">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="4e35b-1593">---- | | `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e35b-1594">İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e35b-1595">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e35b-1596">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="4e35b-1597">| | `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="4e35b-1598">| | `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4e35b-1599">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4e35b-1600">Iletilen üstbilgiler ara yazılımını yapılandıran [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components)ve ASP.NET Core modülü, DÜZENI (http/https) ve isteğin KAYNAKLANDıĞı uzak IP adresini iletecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="4e35b-1601">Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="4e35b-1602">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="4e35b-1603">Web. config dosyası</span><span class="sxs-lookup"><span data-stu-id="4e35b-1603">web.config file</span></span>

<span data-ttu-id="4e35b-1604">*Web. config* dosyası [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e35b-1605">*Web. config* dosyası oluşturma, dönüştürme ve yayımlama, proje yayımlandığında bir MSBuild hedefi () tarafından işlenir `_TransformWebConfig` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="4e35b-1606">Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="4e35b-1607">SDK proje dosyasının en üstünde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4e35b-1608">Bir *Web. config* dosyası projede yoksa, dosya ASP.NET Core modülünü yapılandırmak Için doğru *processPath* ve *bağımsız değişkenlerle* oluşturulur ve [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="4e35b-1609">Projede bir *Web. config* dosyası varsa, dosya ASP.NET Core modülünü yapılandırmak ve yayımlanan çıktıya taşınmak Için doğru *processPath* ve *bağımsız değişkenlerle* birlikte dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="4e35b-1610">Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="4e35b-1611">*Web. config* dosyası, etkin IIS modüllerini DENETLEYEN ek IIS yapılandırma ayarları verebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="4e35b-1612">ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="4e35b-1613">Web SDK 'sının *Web. config* dosyasını dönüştürülmesini engellemek için, **\<IsTransformWebConfigDisabled>** Proje dosyasındaki özelliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="4e35b-1614">Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakıldığında, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="4e35b-1615">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="4e35b-1616">Web. config dosyası konumu</span><span class="sxs-lookup"><span data-stu-id="4e35b-1616">web.config file location</span></span>

<span data-ttu-id="4e35b-1617">[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, *Web. config* dosyası dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="4e35b-1618">Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="4e35b-1619">Web Dağıtımı kullanarak birden çok uygulama yayımlamayı etkinleştirmek için uygulamanın kökünde *Web. config* dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="4e35b-1620">Uygulamanın fiziksel yolunda \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . xml* (XML belge açıklamaları) ve \* \<assembly> . Deps. JSON\*gibi hassas dosyalar bulunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="4e35b-1621">*Web. config* dosyası mevcut olduğunda ve site normal olarak başlatıldığında, istenirse IIS bu hassas dosyaları sunmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="4e35b-1622">*Web. config* dosyası eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="4e35b-1623">***Web. config* dosyasının her zaman dağıtımda mevcut olması, doğru şekilde adlandırılması ve siteyi normal başlangıç için yapılandırabiliyor olması gerekir. *Web. config* dosyasını bir üretim dağıtımından hiçbir şekilde kaldırmayın.**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="4e35b-1624">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="4e35b-1624">Transform web.config</span></span>

<span data-ttu-id="4e35b-1625">*Web. config* 'i yayımlama sırasında dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlayın), bkz <xref:host-and-deploy/iis/transform-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="4e35b-1626">IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-1626">IIS configuration</span></span>

<span data-ttu-id="4e35b-1627">**Windows Server işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="4e35b-1628">**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="4e35b-1629">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi**bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="4e35b-1630">**Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="4e35b-1632">**Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="4e35b-1633">İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="4e35b-1635">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1636">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="4e35b-1637">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e35b-1638">Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e35b-1639">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1640">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e35b-1641">WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="4e35b-1642">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e35b-1643">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e35b-1644">Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="4e35b-1645">**Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="4e35b-1646">**Windows masaüstü işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="4e35b-1647">**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini**etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e35b-1648">**Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="4e35b-1649">**Internet Information Services** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="4e35b-1650">**Web yönetimi araçları** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="4e35b-1651">**IIS Yönetim Konsolu**kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="4e35b-1652">**World Wide Web Hizmetleri**kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e35b-1653">**World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="4e35b-1654">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1655">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="4e35b-1656">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e35b-1657">Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e35b-1658">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e35b-1659">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e35b-1660">WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="4e35b-1661">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e35b-1662">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e35b-1663">IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1663">If the IIS installation requires a restart, restart the system.</span></span>

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="4e35b-1665">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4e35b-1666">*.NET Core barındırma paketi* 'ni barındırma sistemine yükler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="4e35b-1667">Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e35b-1668">Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e35b-1669">Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="4e35b-1670">IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="4e35b-1671">.NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4e35b-1672">Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="4e35b-1673">İndir</span><span class="sxs-lookup"><span data-stu-id="4e35b-1673">Download</span></span>

1. <span data-ttu-id="4e35b-1674">[.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="4e35b-1675">İstediğiniz .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="4e35b-1676">**Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="4e35b-1677">**Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="4e35b-1678">Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="4e35b-1679">Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="4e35b-1680">Barındırma paketini yükler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="4e35b-1681">Yükleyiciyi sunucuda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1681">Run the installer on the server.</span></span> <span data-ttu-id="4e35b-1682">Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="4e35b-1683">`OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="4e35b-1684">`OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="4e35b-1685">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e35b-1686">`OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="4e35b-1687">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e35b-1688">`OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="4e35b-1689">32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="4e35b-1690">Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="4e35b-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma (*ApplicationHost. config*) IIS yüklemesiyle aynı makinada olduğunda, IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="4e35b-1692">*Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="4e35b-1693">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="4e35b-1694">Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="4e35b-1695">IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="4e35b-1696">Barındırma paketini yüklerken IIS 'deki bireysel siteleri el ile durdurmanız gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="4e35b-1697">Barındırılan uygulamalar (IIS siteleri) IIS yeniden başlatıldığında yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="4e35b-1698">Uygulamalar, [uygulama başlatma modülünden](#application-initialization-module-and-idle-timeout)da dahil olmak üzere ilk isteklerini alırken yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="4e35b-1699">ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için ileri sarma davranışını benimseme.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="4e35b-1700">IIS ile IIS tarafından barındırılan uygulamalar IIS ile yeniden başlatıldığında, ilk isteklerini alırken başvurulan paketlerinin en son düzeltme eki sürümleriyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="4e35b-1701">IIS yeniden başlatılırsa, uygulamalar yeniden başlatılır ve çalışan işlemlerine geri dönüştürüldüğünde geri alma davranışını gösterir ve ilk isteklerini alırlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="4e35b-1702">IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="4e35b-1703">Visual Studio ile yayımlarken Web Dağıtımı yüklemesi</span><span class="sxs-lookup"><span data-stu-id="4e35b-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="4e35b-1704">Uygulamaları [Web dağıtımı](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)olan sunuculara dağıttığınızda, en son Web dağıtımı sürümünü sunucuya yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="4e35b-1705">Web Dağıtımı yüklemek için [Web Platformu Yükleyicisi 'ni (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft İndirme Merkezi](https://www.microsoft.com/download/details.aspx?id=43717)'nden bir yükleyici edinin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="4e35b-1706">Tercih edilen yöntem, WebPI kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="4e35b-1707">WebPI, barındırma sağlayıcıları için tek başına kurulum ve yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="4e35b-1708">IIS sitesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1708">Create the IIS site</span></span>

1. <span data-ttu-id="4e35b-1709">Barındırma sisteminde, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="4e35b-1710">Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="4e35b-1711">Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="4e35b-1712">IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="4e35b-1713">**Siteler** klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="4e35b-1714">Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="4e35b-1715">Bir **site adı** belirtin ve **fiziksel yolu** uygulamanın dağıtım klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="4e35b-1716">**Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Web sitesi Ekle adımında site adı, fiziksel yol ve ana bilgisayar adını sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="4e35b-1718">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4e35b-1719">Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="4e35b-1720">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4e35b-1721">Joker karakterler yerine açık ana bilgisayar adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="4e35b-1722">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4e35b-1723">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="4e35b-1724">Sunucu düğümünün altında **uygulama havuzları**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="4e35b-1725">Sitenin uygulama havuzuna sağ tıklayın ve bağlam menüsünden **temel ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="4e35b-1726">**Uygulama havuzunu Düzenle** penceresinde, **.NET CLR sürümünü** **yönetilen kod olmadan**ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR sürümü için yönetilen kod ayarlama.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="4e35b-1728">ASP.NET Core ayrı bir işlemde çalışır ve çalışma zamanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="4e35b-1729">ASP.NET Core, masaüstü CLR 'nin (.NET CLR) yüklenmemesine bağlı değildir &mdash; . .NET Core Için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı çalışan işlemde barındırmak için önyüklenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="4e35b-1730">**.NET CLR sürümünün** **yönetilen kod olmadan** ayarlanması isteğe bağlıdır, ancak önerilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="4e35b-1731">*ASP.NET Core 2,2 veya üzeri*: [işlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [tabanlı bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, 32-bit (x86) işlemleri için uygulama havuzunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="4e35b-1732">IIS Yöneticisi > **uygulama havuzlarının** **Eylemler** kenar çubuğunda, **uygulama havuzu varsayılanlarını** veya **Gelişmiş ayarları**ayarla ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="4e35b-1733">**32 bitlik uygulamaları etkinleştir** ' i bulun ve değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="4e35b-1734">Bu ayar [, işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="4e35b-1735">İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="4e35b-1736">Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) **applicationpokaydentity** 'den başka bir kimliğe değiştiyse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="4e35b-1737">Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="4e35b-1738">**Windows kimlik doğrulama yapılandırması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="4e35b-1739">Daha fazla bilgi için bkz. [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="4e35b-1740">Uygulamayı dağıtma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1740">Deploy the app</span></span>

<span data-ttu-id="4e35b-1741">Uygulamayı [IIS sitesi oluşturma](#create-the-iis-site) bölümünde oluşturulan IIS **fiziksel yol** klasörüne dağıtın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="4e35b-1742">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *Publish* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="4e35b-1743">Visual Studio ile Web Dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="4e35b-1744">Web Dağıtımı kullanılacak bir yayımlama profili oluşturma hakkında bilgi edinmek için bkz. [ASP.NET Core App Deployment Için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="4e35b-1745">Barındırma sağlayıcısı, bir yayımlama profili veya oluşturma desteği sağlıyorsa, profilini indirin ve Visual Studio **Yayımlama** iletişim kutusunu kullanarak içeri aktarın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Yayımla iletişim sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="4e35b-1747">Visual Studio dışında Web Dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="4e35b-1748">[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) , komut satırından Visual Studio dışında da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="4e35b-1749">Daha fazla bilgi için bkz. [Web Dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="4e35b-1750">Web Dağıtımı alternatifleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="4e35b-1751">Uygulamayı barındırma sistemine taşımak için el ile kopyalama, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi çeşitli yöntemlerden birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="4e35b-1752">IIS 'ye dağıtım ASP.NET Core hakkında daha fazla bilgi için, [IIS yöneticileri Için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="4e35b-1753">Web sitesine gidin</span><span class="sxs-lookup"><span data-stu-id="4e35b-1753">Browse the website</span></span>

<span data-ttu-id="4e35b-1754">Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın genel uç noktalarından birine bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="4e35b-1755">Aşağıdaki örnekte, site bağlantı noktasındaki IIS **ana bilgisayar adına** bağlıdır `www.mysite.com` **Port** `80` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="4e35b-1756">Bir istek şu şekilde yapılır `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="4e35b-1756">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge tarayıcısı, IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="4e35b-1758">Kilitli dağıtım dosyaları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1758">Locked deployment files</span></span>

<span data-ttu-id="4e35b-1759">Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="4e35b-1760">Dağıtım sırasında kilitli dosyaların üzerine yazılamaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="4e35b-1761">Kilitli dosyaları bir dağıtımda serbest bırakmak için aşağıdaki yaklaşımlardan **birini** kullanarak uygulama havuzunu durdurun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="4e35b-1762">Proje dosyasında Web Dağıtımı ve başvurusunu kullanın `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="4e35b-1763">Bir *app_offline. htm* dosyası Web uygulaması dizininin köküne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="4e35b-1764">Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="4e35b-1765">Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="4e35b-1766">Sunucu üzerindeki IIS Yöneticisi 'nde uygulama havuzunu el ile durdurun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="4e35b-1767">*App_offline. htm* dosyasını bırakmak için PowerShell kullanın (PowerShell 5 veya üzerini gerektirir):</span><span class="sxs-lookup"><span data-stu-id="4e35b-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="4e35b-1768">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="4e35b-1768">Data protection</span></span>

<span data-ttu-id="4e35b-1769">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="4e35b-1770">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="4e35b-1771">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="4e35b-1772">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="4e35b-1773">Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçleri geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="4e35b-1774">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="4e35b-1775">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="4e35b-1776">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata)içerebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="4e35b-1777">Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="4e35b-1778">**Veri koruma kayıt defteri anahtarları oluşturma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="4e35b-1779">ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="4e35b-1780">Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="4e35b-1781">Tek başına, Web grubu olmayan IIS yüklemeleri için [Data Protection provision-AutoGenKeys. ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="4e35b-1782">Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="4e35b-1783">Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="4e35b-1784">Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="4e35b-1785">Varsayılan olarak, veri koruma anahtarları şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="4e35b-1786">Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştırıldığı Windows hesabıyla sınırlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="4e35b-1787">Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="4e35b-1788">Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün: sertifikaları kullanıcının güvenilen sertifika deposuna yerleştir ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="4e35b-1789">Ayrıntılar için bkz. [ASP.NET Core veri korumasını yapılandırma](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="4e35b-1790">**Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="4e35b-1791">Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="4e35b-1792">**Kullanıcı profilini yükle** ' ye ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="4e35b-1793">Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="4e35b-1794">Anahtarlar *% LocalAppData%/ASP.exe. net/DataProtection-Keys* klasörüne kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="4e35b-1795">Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="4e35b-1796">Varsayılan değeri `setProfileEnvironment` `true` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="4e35b-1797">Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="4e35b-1798">Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="4e35b-1799">*% Windir%/system32/inetsrv/config* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="4e35b-1800">*ApplicationHost. config* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="4e35b-1801">Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="4e35b-1802">`setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="4e35b-1803">**Dosya sistemini anahtar halka deposu olarak kullanma**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="4e35b-1804">[Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="4e35b-1805">Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="4e35b-1806">Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="4e35b-1807">IIS 'yi bir Web grubunda kullanırken:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="4e35b-1808">Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="4e35b-1809">Her makineye bir x509 sertifikası dağıtın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="4e35b-1810">[Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="4e35b-1811">**Veri koruma için makineye özel bir ilke ayarlama**</span><span class="sxs-lookup"><span data-stu-id="4e35b-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="4e35b-1812">Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="4e35b-1813">Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="4e35b-1814">Sanal Dizinler</span><span class="sxs-lookup"><span data-stu-id="4e35b-1814">Virtual Directories</span></span>

<span data-ttu-id="4e35b-1815">[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="4e35b-1816">Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="4e35b-1817">Alt uygulamalar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1817">Sub-applications</span></span>

<span data-ttu-id="4e35b-1818">ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="4e35b-1819">Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="4e35b-1820">Bir alt uygulama işleyici olarak ASP.NET Core modülünü içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="4e35b-1821">Modül bir alt uygulamanın *Web. config* dosyasına bir işleyici olarak eklenirse, alt uygulamaya gözatmaya çalışılırken hatalı yapılandırma dosyasına başvuran *500,19 iç sunucu hatası* alınır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="4e35b-1822">Aşağıdaki örnek, bir ASP.NET Core alt uygulaması için yayımlanmış bir *Web. config* dosyası gösterir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="4e35b-1823">Bir ASP.NET Core uygulamasının altında bir non-ASP.NET Core alt uygulaması barındırırken, alt uygulamanın *Web. config* dosyasında devralınan işleyiciyi açıkça kaldırın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="4e35b-1824">Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="4e35b-1825">Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="4e35b-1826">Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="4e35b-1827">Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="4e35b-1828">İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="4e35b-1829">Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="4e35b-1830">Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="4e35b-1831">Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="4e35b-1832">Alt uygulama için bir uygulama havuzu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="4e35b-1833">.NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="4e35b-1834">Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="4e35b-1835">IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="4e35b-1836">**Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="4e35b-1837">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1837">Select **OK**.</span></span>

<span data-ttu-id="4e35b-1838">Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="4e35b-1839">İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="4e35b-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="4e35b-1840">Web. config ile IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4e35b-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="4e35b-1841">IIS yapılandırması, `<system.webServer>` ASP.NET Core modüllü ASP.NET Core uygulamalar için işlevsel olan IIS senaryoları için *Web. config* 'in bölümü tarafından etkilenir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="4e35b-1842">Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="4e35b-1843">IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulamanın *Web. config* dosyasındaki öğesi ASP.NET Core bir uygulama için devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="4e35b-1844">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="4e35b-1845">İçin yapılandırma başvurusu\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="4e35b-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="4e35b-1846">Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri \<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *Appcmd. exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="4e35b-1847">Web. config 'in yapılandırma bölümleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="4e35b-1848">*Web. config* dosyasındaki ASP.NET 4. x uygulamalarının yapılandırma bölümleri yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmaz:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="4e35b-1849">ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="4e35b-1850">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="4e35b-1851">Uygulama havuzları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1851">Application Pools</span></span>

<span data-ttu-id="4e35b-1852">Bir sunucuda birden çok Web sitesi barındırırken, her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="4e35b-1853">IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak bu yapılandırmaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="4e35b-1854">Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="4e35b-1855">Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="4e35b-1856">Uygulama havuzuIdentity</span><span class="sxs-lookup"><span data-stu-id="4e35b-1856">Application Pool Identity</span></span>

<span data-ttu-id="4e35b-1857">Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="4e35b-1858">IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="4e35b-1859">Uygulama havuzunun **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu 'nda, uygulamasının **Identity** **applicationpokaydentity**kullanacak şekilde ayarlandığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

<span data-ttu-id="4e35b-1861">IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="4e35b-1862">Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="4e35b-1863">Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="4e35b-1864">IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="4e35b-1865">Windows Gezgini 'ni açın ve dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="4e35b-1866">Dizine sağ tıklayıp **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="4e35b-1867">**Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="4e35b-1868">**Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="4e35b-1869">**Seçilecek nesne adlarını girin** alanına \*\* \\ app_pool_name>IIS AppPool<\*\* girin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="4e35b-1870">**Adları denetle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="4e35b-1871">*DefaultAppPool* için **IIS APPPOOL\DefaultAppPool**kullanarak adları denetleyin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="4e35b-1872">**Adları denetle** düğmesi seçildiğinde, nesne adları alanında bir **DefaultAppPool** değeri gösterilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="4e35b-1873">Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="4e35b-1874">Nesne adı denetlenirken **IIS AppPool \\<APP_POOL_NAME>** biçimini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="4e35b-1876">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1876">Select **OK**.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="4e35b-1878">Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="4e35b-1879">Gerektiğinde ek izinler sağlayın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="4e35b-1880">Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="4e35b-1881">Örnek olarak *DefaultAppPool* kullanarak, aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="4e35b-1882">Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4e35b-1883">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="4e35b-1883">HTTP/2 support</span></span>

<span data-ttu-id="4e35b-1884">[Http/2](https://httpwg.org/specs/rfc7540.html) , aşağıdaki temel gereksinimleri karşılayan işlem dışı dağıtımlar için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="4e35b-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="4e35b-1885">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="4e35b-1886">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="4e35b-1887">Hedef Framework: HTTP/2 bağlantısı tamamen IIS tarafından işlendiğinden, işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="4e35b-1888">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4e35b-1889">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="4e35b-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="4e35b-1890">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4e35b-1891">HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="4e35b-1892">IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="4e35b-1893">CORS ön denetim istekleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1893">CORS preflight requests</span></span>

<span data-ttu-id="4e35b-1894">*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="4e35b-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="4e35b-1895">.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="4e35b-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="4e35b-1896">*Web. config* DOSYASıNDAKI uygulama IIS işleyicilerini seçenek isteklerini geçecek şekilde yapılandırma hakkında bilgi edinmek için bkz. [ASP.NET Web API 2 ' de çapraz kaynak ISTEKLERINI etkinleştirme: CORS 'nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="4e35b-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="4e35b-1897">IIS yöneticileri için dağıtım kaynakları</span><span class="sxs-lookup"><span data-stu-id="4e35b-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="4e35b-1898">IIS belgeleri</span><span class="sxs-lookup"><span data-stu-id="4e35b-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="4e35b-1899">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="4e35b-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="4e35b-1900">.NET Core uygulama dağıtımı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="4e35b-1901">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4e35b-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="4e35b-1902">Resmi Microsoft IIS sitesi</span><span class="sxs-lookup"><span data-stu-id="4e35b-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="4e35b-1903">Windows Server teknik içerik kitaplığı</span><span class="sxs-lookup"><span data-stu-id="4e35b-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="4e35b-1904">IIS üzerinde HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4e35b-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
