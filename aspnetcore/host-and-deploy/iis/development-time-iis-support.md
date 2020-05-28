---
<span data-ttu-id="177b1-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="177b1-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="177b1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="177b1-102">'Blazor'</span></span>
- <span data-ttu-id="177b1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="177b1-103">'Identity'</span></span>
- <span data-ttu-id="177b1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="177b1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="177b1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="177b1-105">'Razor'</span></span>
- <span data-ttu-id="177b1-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="177b1-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="177b1-107">ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği</span><span class="sxs-lookup"><span data-stu-id="177b1-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="177b1-108">, [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="177b1-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="177b1-109">Bu makalede, Windows Server 'da IIS ile çalışan ASP.NET Core hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="177b1-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="177b1-110">Bu konu başlığı altında, bu senaryonun etkinleştirilmesi ve bir projenin kurulması anlatılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="177b1-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="177b1-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="177b1-111">Prerequisites</span></span>

* [<span data-ttu-id="177b1-112">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="177b1-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="177b1-113">**ASP.net ve Web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="177b1-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="177b1-114">**.NET Core platformlar arası geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="177b1-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="177b1-115">X. 509.440 güvenlik sertifikası (HTTPS desteği için)</span><span class="sxs-lookup"><span data-stu-id="177b1-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="177b1-116">IIS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="177b1-116">Enable IIS</span></span>

1. <span data-ttu-id="177b1-117">Windows 'ta, **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafı) bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="177b1-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="177b1-118">**Internet Information Services** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="177b1-119">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-119">Select **OK**.</span></span>

<span data-ttu-id="177b1-120">IIS yüklemesi için sistemin yeniden başlatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="177b1-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="177b1-121">IIS hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="177b1-121">Configure IIS</span></span>

<span data-ttu-id="177b1-122">IIS 'nin aşağıdaki ile yapılandırılmış bir Web sitesine sahip olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="177b1-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="177b1-123">**Ana bilgisayar adı**: genellikle **varsayılan Web sitesi** , bir **ana bilgisayar adıyla** kullanılır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="177b1-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="177b1-124">Ancak, benzersiz bir ana bilgisayar adına sahip geçerli bir IIS Web sitesi çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="177b1-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="177b1-125">**Site bağlama**</span><span class="sxs-lookup"><span data-stu-id="177b1-125">**Site Binding**</span></span>
  * <span data-ttu-id="177b1-126">HTTPS gerektiren uygulamalar için, sertifika ile 443 numaralı bağlantı noktasına bir bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="177b1-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="177b1-127">Genellikle **IIS Express geliştirme sertifikası** kullanılır, ancak geçerli bir sertifika çalışıyor olur.</span><span class="sxs-lookup"><span data-stu-id="177b1-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="177b1-128">HTTP kullanan uygulamalar için, 80 veya yeni bir site için bağlantı noktası 80 ' e bir bağlama oluşturmak için bir bağlamanın mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="177b1-129">HTTP veya HTTPS için tek bir bağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="177b1-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="177b1-130">**Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="177b1-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="177b1-131">Visual Studio 'da geliştirme zamanı IIS desteğini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="177b1-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="177b1-132">Visual Studio yükleyicisi 'ni başlatın.</span><span class="sxs-lookup"><span data-stu-id="177b1-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="177b1-133">IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="177b1-134">**ASP.net ve Web geliştirme** iş yükü için **geliştirme zamanı IIS destek** bileşeni ' ni bulun ve yükler.</span><span class="sxs-lookup"><span data-stu-id="177b1-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="177b1-135">Bileşen, iş yüklerinin sağındaki **Yükleme ayrıntıları** panelinde, **geliştirme zamanı IIS desteği** altındaki **isteğe bağlı** bölümde listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="177b1-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="177b1-136">Bileşen, IIS ile ASP.NET Core uygulamaları çalıştırmak için gerekli yerel bir IIS modülü olan [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="177b1-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="177b1-137">Projeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="177b1-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="177b1-138">HTTPS yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="177b1-138">HTTPS redirection</span></span>

<span data-ttu-id="177b1-139">HTTPS gerektiren yeni bir proje için **yeni ASP.NET Core Web uygulaması oluşturma** penceresinde **https için yapılandırmak** üzere onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="177b1-140">Onay kutusunun belirlenmesi, uygulama oluşturulduğunda [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) ekler.</span><span class="sxs-lookup"><span data-stu-id="177b1-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="177b1-141">HTTPS gerektiren mevcut bir proje için ' de HTTPS yeniden yönlendirme ve HSTS ara yazılımı kullanın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="177b1-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="177b1-142">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="177b1-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="177b1-143">HTTP kullanan bir proje için, [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) uygulamaya eklenmez.</span><span class="sxs-lookup"><span data-stu-id="177b1-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="177b1-144">Uygulama yapılandırması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="177b1-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="177b1-145">IIS başlatma profili</span><span class="sxs-lookup"><span data-stu-id="177b1-145">IIS launch profile</span></span>

<span data-ttu-id="177b1-146">Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="177b1-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="177b1-147">**Çözüm Gezgini**projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="177b1-148">**Özellikler**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-148">Select **Properties**.</span></span> <span data-ttu-id="177b1-149">**Hata Ayıkla** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="177b1-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="177b1-150">**Profil**için **Yeni** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="177b1-151">Profili, açılan pencerede "IIS" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="177b1-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="177b1-152">Profili oluşturmak için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="177b1-153">**Başlatma** ayarı Için listeden **IIS** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="177b1-154">**Başlat tarayıcısı** onay kutusunu seçin ve uç nokta URL 'sini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="177b1-155">Uygulama HTTPS gerektirdiğinde, bir HTTPS uç noktası () kullanın `https://` .</span><span class="sxs-lookup"><span data-stu-id="177b1-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="177b1-156">HTTP için bir HTTP ( `http://` ) uç noktası kullanın.</span><span class="sxs-lookup"><span data-stu-id="177b1-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="177b1-157">[Daha önce belirtilen IIS yapılandırmasıyla](#configure-iis)aynı ana bilgisayar adını ve bağlantı noktasını (genellikle) sağlayın `localhost` .</span><span class="sxs-lookup"><span data-stu-id="177b1-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="177b1-158">URL 'nin sonundaki uygulamanın adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="177b1-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="177b1-159">Örneğin, `https://localhost/WebApplication1` (https) veya `http://localhost/WebApplication1` (http) geçerli uç nokta URL 'lardır.</span><span class="sxs-lookup"><span data-stu-id="177b1-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="177b1-160">**Ortam değişkenleri** bölümünde **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="177b1-161">**Adı** ve değeri olan bir ortam değişkeni sağlayın `ASPNETCORE_ENVIRONMENT` **Value** `Development` .</span><span class="sxs-lookup"><span data-stu-id="177b1-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="177b1-162">**Web sunucusu ayarları** alanında, **Uygulama URL** 'sini **başlatma tarayıcısı** uç noktası URL 'si için kullanılan aynı değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="177b1-163">Visual Studio 2019 veya sonraki sürümlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak üzere **varsayılan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="177b1-164">Proje, `<AspNetCoreHostingModel>` Proje dosyasında özelliği ayarlarsa, özelliğin değeri ( `InProcess` veya `OutOfProcess` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="177b1-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="177b1-165">Özellik mevcut değilse, uygulamanın varsayılan barındırma modeli, işlem içi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="177b1-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="177b1-166">Uygulama, uygulamanın normal barındırma modelinden farklı bir açık barındırma modeli ayarı gerektiriyorsa, **barındırma modelini** gereken ya da gerektiği şekilde ayarlayın `In Process` `Out Of Process` .</span><span class="sxs-lookup"><span data-stu-id="177b1-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="177b1-167">Profili kaydedin.</span><span class="sxs-lookup"><span data-stu-id="177b1-167">Save the profile.</span></span>

<span data-ttu-id="177b1-168">Visual Studio kullanmadığınız durumlarda, *Özellikler* klasöründeki [launchsettings. JSON](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="177b1-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="177b1-169">Aşağıdaki örnek, HTTPS protokolünü kullanmak için profili yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="177b1-169">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="177b1-170">`applicationUrl`Ve `launchUrl` bitiş noktalarının EŞLEŞTIĞINI ve IIS bağlama yapılandırmasıyla aynı Protokolü (http veya https) kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="177b1-171">Projeyi çalıştırma</span><span class="sxs-lookup"><span data-stu-id="177b1-171">Run the project</span></span>

<span data-ttu-id="177b1-172">Visual Studio 'Yu yönetici olarak çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="177b1-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="177b1-173">Derleme yapılandırması açılır listesinin **hata ayıklama**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="177b1-174">[Hata ayıklamayı Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="177b1-175">Visual Studio, yönetici olarak çalışmıyorsa bir yeniden başlatma isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="177b1-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="177b1-176">İstenirse, Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="177b1-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="177b1-177">Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="177b1-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="177b1-178">[Yalnızca kendi kodum](/visualstudio/debugger/just-my-code) ve derleyici Iyileştirmeleriyle yayın derleme yapılandırması hata ayıklaması, düzeyi düşürülmüş bir deneyimle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="177b1-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="177b1-179">Örneğin, kesme noktaları isabet edilmez.</span><span class="sxs-lookup"><span data-stu-id="177b1-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="177b1-180">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="177b1-180">Additional resources</span></span>

* [<span data-ttu-id="177b1-181">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="177b1-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="177b1-182">Bu makalede, Windows Server 'da IIS ile çalışan ASP.NET Core hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="177b1-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="177b1-183">Bu konu başlığı altında, bu senaryonun etkinleştirilmesi ve bir projenin kurulması anlatılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="177b1-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="177b1-184">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="177b1-184">Prerequisites</span></span>

* [<span data-ttu-id="177b1-185">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="177b1-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="177b1-186">**ASP.net ve Web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="177b1-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="177b1-187">**.NET Core platformlar arası geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="177b1-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="177b1-188">X. 509.440 güvenlik sertifikası (HTTPS desteği için)</span><span class="sxs-lookup"><span data-stu-id="177b1-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="177b1-189">IIS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="177b1-189">Enable IIS</span></span>

1. <span data-ttu-id="177b1-190">Windows 'ta, **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafı) bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="177b1-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="177b1-191">**Internet Information Services** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="177b1-192">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-192">Select **OK**.</span></span>

<span data-ttu-id="177b1-193">IIS yüklemesi için sistemin yeniden başlatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="177b1-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="177b1-194">IIS hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="177b1-194">Configure IIS</span></span>

<span data-ttu-id="177b1-195">IIS 'nin aşağıdaki ile yapılandırılmış bir Web sitesine sahip olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="177b1-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="177b1-196">**Ana bilgisayar adı**: genellikle **varsayılan Web sitesi** , bir **ana bilgisayar adıyla** kullanılır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="177b1-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="177b1-197">Ancak, benzersiz bir ana bilgisayar adına sahip geçerli bir IIS Web sitesi çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="177b1-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="177b1-198">**Site bağlama**</span><span class="sxs-lookup"><span data-stu-id="177b1-198">**Site Binding**</span></span>
  * <span data-ttu-id="177b1-199">HTTPS gerektiren uygulamalar için, sertifika ile 443 numaralı bağlantı noktasına bir bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="177b1-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="177b1-200">Genellikle **IIS Express geliştirme sertifikası** kullanılır, ancak geçerli bir sertifika çalışıyor olur.</span><span class="sxs-lookup"><span data-stu-id="177b1-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="177b1-201">HTTP kullanan uygulamalar için, 80 veya yeni bir site için bağlantı noktası 80 ' e bir bağlama oluşturmak için bir bağlamanın mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="177b1-202">HTTP veya HTTPS için tek bir bağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="177b1-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="177b1-203">**Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="177b1-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="177b1-204">Visual Studio 'da geliştirme zamanı IIS desteğini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="177b1-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="177b1-205">Visual Studio yükleyicisi 'ni başlatın.</span><span class="sxs-lookup"><span data-stu-id="177b1-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="177b1-206">IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="177b1-207">**ASP.net ve Web geliştirme** iş yükü için **geliştirme zamanı IIS destek** bileşeni ' ni bulun ve yükler.</span><span class="sxs-lookup"><span data-stu-id="177b1-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="177b1-208">Bileşen, iş yüklerinin sağındaki **Yükleme ayrıntıları** panelinde, **geliştirme zamanı IIS desteği** altındaki **isteğe bağlı** bölümde listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="177b1-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="177b1-209">Bileşen, IIS ile ASP.NET Core uygulamaları çalıştırmak için gerekli yerel bir IIS modülü olan [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="177b1-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="177b1-210">Projeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="177b1-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="177b1-211">HTTPS yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="177b1-211">HTTPS redirection</span></span>

<span data-ttu-id="177b1-212">HTTPS gerektiren yeni bir proje için **yeni ASP.NET Core Web uygulaması oluşturma** penceresinde **https için yapılandırmak** üzere onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="177b1-213">Onay kutusunun belirlenmesi, uygulama oluşturulduğunda [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) ekler.</span><span class="sxs-lookup"><span data-stu-id="177b1-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="177b1-214">HTTPS gerektiren mevcut bir proje için ' de HTTPS yeniden yönlendirme ve HSTS ara yazılımı kullanın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="177b1-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="177b1-215">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="177b1-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="177b1-216">HTTP kullanan bir proje için, [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) uygulamaya eklenmez.</span><span class="sxs-lookup"><span data-stu-id="177b1-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="177b1-217">Uygulama yapılandırması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="177b1-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="177b1-218">IIS başlatma profili</span><span class="sxs-lookup"><span data-stu-id="177b1-218">IIS launch profile</span></span>

<span data-ttu-id="177b1-219">Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="177b1-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="177b1-220">**Çözüm Gezgini**projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="177b1-221">**Özellikler**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-221">Select **Properties**.</span></span> <span data-ttu-id="177b1-222">**Hata Ayıkla** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="177b1-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="177b1-223">**Profil**için **Yeni** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="177b1-224">Profili, açılan pencerede "IIS" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="177b1-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="177b1-225">Profili oluşturmak için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="177b1-226">**Başlatma** ayarı Için listeden **IIS** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="177b1-227">**Başlat tarayıcısı** onay kutusunu seçin ve uç nokta URL 'sini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="177b1-228">Uygulama HTTPS gerektirdiğinde, bir HTTPS uç noktası () kullanın `https://` .</span><span class="sxs-lookup"><span data-stu-id="177b1-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="177b1-229">HTTP için bir HTTP ( `http://` ) uç noktası kullanın.</span><span class="sxs-lookup"><span data-stu-id="177b1-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="177b1-230">[Daha önce belirtilen IIS yapılandırmasıyla](#configure-iis)aynı ana bilgisayar adını ve bağlantı noktasını (genellikle) sağlayın `localhost` .</span><span class="sxs-lookup"><span data-stu-id="177b1-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="177b1-231">URL 'nin sonundaki uygulamanın adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="177b1-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="177b1-232">Örneğin, `https://localhost/WebApplication1` (https) veya `http://localhost/WebApplication1` (http) geçerli uç nokta URL 'lardır.</span><span class="sxs-lookup"><span data-stu-id="177b1-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="177b1-233">**Ortam değişkenleri** bölümünde **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="177b1-234">**Adı** ve değeri olan bir ortam değişkeni sağlayın `ASPNETCORE_ENVIRONMENT` **Value** `Development` .</span><span class="sxs-lookup"><span data-stu-id="177b1-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="177b1-235">**Web sunucusu ayarları** alanında, **Uygulama URL** 'sini **başlatma tarayıcısı** uç noktası URL 'si için kullanılan aynı değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="177b1-236">Visual Studio 2019 veya sonraki sürümlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak üzere **varsayılan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="177b1-237">Proje, `<AspNetCoreHostingModel>` Proje dosyasında özelliği ayarlarsa, özelliğin değeri ( `InProcess` veya `OutOfProcess` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="177b1-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="177b1-238">Özellik mevcut değilse, uygulamanın varsayılan barındırma modeli kullanılır ve bu işlem, işlem dışı olur.</span><span class="sxs-lookup"><span data-stu-id="177b1-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="177b1-239">Uygulama, uygulamanın normal barındırma modelinden farklı bir açık barındırma modeli ayarı gerektiriyorsa, **barındırma modelini** gereken ya da gerektiği şekilde ayarlayın `In Process` `Out Of Process` .</span><span class="sxs-lookup"><span data-stu-id="177b1-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="177b1-240">Profili kaydedin.</span><span class="sxs-lookup"><span data-stu-id="177b1-240">Save the profile.</span></span>

<span data-ttu-id="177b1-241">Visual Studio kullanmadığınız durumlarda, *Özellikler* klasöründeki [launchsettings. JSON](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="177b1-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="177b1-242">Aşağıdaki örnek, HTTPS protokolünü kullanmak için profili yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="177b1-242">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="177b1-243">`applicationUrl`Ve `launchUrl` bitiş noktalarının EŞLEŞTIĞINI ve IIS bağlama yapılandırmasıyla aynı Protokolü (http veya https) kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="177b1-244">Projeyi çalıştırma</span><span class="sxs-lookup"><span data-stu-id="177b1-244">Run the project</span></span>

<span data-ttu-id="177b1-245">Visual Studio 'Yu yönetici olarak çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="177b1-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="177b1-246">Derleme yapılandırması açılır listesinin **hata ayıklama**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="177b1-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="177b1-247">[Hata ayıklamayı Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="177b1-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="177b1-248">Visual Studio, yönetici olarak çalışmıyorsa bir yeniden başlatma isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="177b1-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="177b1-249">İstenirse, Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="177b1-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="177b1-250">Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="177b1-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="177b1-251">[Yalnızca kendi kodum](/visualstudio/debugger/just-my-code) ve derleyici Iyileştirmeleriyle yayın derleme yapılandırması hata ayıklaması, düzeyi düşürülmüş bir deneyimle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="177b1-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="177b1-252">Örneğin, kesme noktaları isabet edilmez.</span><span class="sxs-lookup"><span data-stu-id="177b1-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="177b1-253">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="177b1-253">Additional resources</span></span>

* [<span data-ttu-id="177b1-254">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="177b1-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
