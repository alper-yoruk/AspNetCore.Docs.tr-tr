---
title: ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği
author: rick-anderson
description: Windows Server'da IIS ile çalışırken ASP.NET Core uygulamaları nın hata ayıklama desteğini keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f87a1d8cf41248f14932908c0633f98a7198853f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664047"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="d8995-103">ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği</span><span class="sxs-lookup"><span data-stu-id="d8995-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="d8995-104">Yazar: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="d8995-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8995-105">Bu makalede, Windows Server'da IIS ile çalışan ASP.NET Core uygulamaları hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d8995-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="d8995-106">Bu konu, bu senaryoyu etkinleştirme ve bir proje ayarlama yoluyla yürür.</span><span class="sxs-lookup"><span data-stu-id="d8995-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8995-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d8995-107">Prerequisites</span></span>

* [<span data-ttu-id="d8995-108">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8995-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="d8995-109">**ASP.NET ve web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="d8995-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="d8995-110">**.NET Core çapraz platform geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="d8995-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="d8995-111">X.509 güvenlik sertifikası (HTTPS desteği için)</span><span class="sxs-lookup"><span data-stu-id="d8995-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="d8995-112">IIS'yi etkinleştir</span><span class="sxs-lookup"><span data-stu-id="d8995-112">Enable IIS</span></span>

1. <span data-ttu-id="d8995-113">Windows'da **Denetim Masası** > **Programları** > **ve Özellikleri** > **Windows özelliklerini açık veya kapalı** olarak açın (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="d8995-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="d8995-114">Internet **Bilgi Hizmetleri** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="d8995-115">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-115">Select **OK**.</span></span>

<span data-ttu-id="d8995-116">IIS yüklemesi bir sistemin yeniden başlatılmasını gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="d8995-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="d8995-117">IIS hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d8995-117">Configure IIS</span></span>

<span data-ttu-id="d8995-118">IIS'nin aşağıdakilerle yapılandırılmış bir web sitesi olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="d8995-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="d8995-119">**Ana bilgisayar adı** &ndash; Genellikle, **Varsayılan Web Sitesi** Ana Bilgisayar **adı** ile `localhost`kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d8995-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="d8995-120">Ancak, benzersiz bir ana bilgisayar adı ile herhangi bir geçerli IIS web sitesi çalışır.</span><span class="sxs-lookup"><span data-stu-id="d8995-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="d8995-121">**Site Bağlama**</span><span class="sxs-lookup"><span data-stu-id="d8995-121">**Site Binding**</span></span>
  * <span data-ttu-id="d8995-122">HTTPS gerektiren uygulamalar için, sertifikalı 443 bağlantı noktasına bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d8995-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="d8995-123">Genellikle, **IIS Express Geliştirme Sertifikası** kullanılır, ancak geçerli sertifika çalışır.</span><span class="sxs-lookup"><span data-stu-id="d8995-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="d8995-124">HTTP kullanan uygulamalar için, 80'i göndermek için bir bağlamanın varlığını onaylayın veya yeni bir site için bağlantı noktası 80'e bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d8995-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="d8995-125">HTTP veya HTTPS için tek bir bağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8995-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="d8995-126">**Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="d8995-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="d8995-127">Visual Studio'da geliştirme zamanı IIS desteğini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="d8995-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="d8995-128">Visual Studio yükleyicisini başlatın.</span><span class="sxs-lookup"><span data-stu-id="d8995-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="d8995-129">IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="d8995-130">ASP.NET **ve web geliştirme** iş yükü için **Geliştirme süresi IIS destek** bileşenini bulun ve yükleyin.</span><span class="sxs-lookup"><span data-stu-id="d8995-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="d8995-131">Bileşen, yükleme **ayrıntıları** panelinde iş yüklerinin sağındaki **Geliştirme süresi IIS desteğiyle** **İsteğe Bağlı** bölümde listelenir.</span><span class="sxs-lookup"><span data-stu-id="d8995-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="d8995-132">Bileşen, IIS ile Core uygulamalarını çalıştırmak için gerekli olan yerel bir IIS modülü olan [ASP.NET Çekirdek Modül'ASP.NET](xref:host-and-deploy/aspnet-core-module)yükler.</span><span class="sxs-lookup"><span data-stu-id="d8995-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="d8995-133">Projeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d8995-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="d8995-134">HTTPS yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="d8995-134">HTTPS redirection</span></span>

<span data-ttu-id="d8995-135">HTTPS gerektiren yeni bir proje için, Yeni bir ASP.NET **Çekirdek Web Uygulaması Oluşturma** penceresinde HTTPS için **Yapılandırmak için** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="d8995-136">Onay kutusunu seçmek, oluşturulduğunda uygulamaya [HTTPS Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) ekler.</span><span class="sxs-lookup"><span data-stu-id="d8995-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="d8995-137">HTTPS gerektiren varolan bir proje için, HTTPS Yeniden Yönlendirme `Startup.Configure`ve HSTS Middleware'i .'da kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8995-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="d8995-138">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="d8995-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="d8995-139">HTTP kullanan bir proje için, [HTTPS Yeniden Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) uygulamaya eklenmez.</span><span class="sxs-lookup"><span data-stu-id="d8995-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="d8995-140">Uygulama yapılandırması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d8995-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="d8995-141">IIS fırlatma profili</span><span class="sxs-lookup"><span data-stu-id="d8995-141">IIS launch profile</span></span>

<span data-ttu-id="d8995-142">Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d8995-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="d8995-143">**Çözüm Gezgini'ndeki**projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="d8995-144">**Özellikleri**seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-144">Select **Properties**.</span></span> <span data-ttu-id="d8995-145">Hata **Ayıklama** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="d8995-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="d8995-146">**Profil**için **Yeni** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="d8995-147">Açılır penceredeki "IIS" profilini adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d8995-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="d8995-148">Profili oluşturmak için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="d8995-149">**Başlatma** ayarı için listeden **IIS'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="d8995-150">**Başlat tarayıcısı** için onay kutusunu seçin ve bitiş noktası URL'sini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="d8995-151">Uygulama HTTPS gerektiriyorsa, bir HTTPS`https://`bitiş noktası ().</span><span class="sxs-lookup"><span data-stu-id="d8995-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="d8995-152">HTTP için bir HTTP`http://`( ) bitiş noktası kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8995-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="d8995-153">Önceki kullanımları belirtilen [IIS yapılandırması](#configure-iis)ile aynı ana `localhost`bilgisayar adı ve bağlantı noktasını sağlayın , genellikle .</span><span class="sxs-lookup"><span data-stu-id="d8995-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="d8995-154">URL'nin sonunda uygulamanın adını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="d8995-155">Örneğin, `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` veya (HTTP) geçerli uç nokta URL'leridir.</span><span class="sxs-lookup"><span data-stu-id="d8995-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="d8995-156">Çevre **değişkenleri** bölümünde **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="d8995-157">**Bir Ad** `ASPNETCORE_ENVIRONMENT` ve **Değeri** olan bir `Development`ortam değişkeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="d8995-158">Web **Sunucusu Ayarları** alanında, **Uygulama URL'sini** **Başlat tarayıcıuç** noktası URL'si için kullanılan değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="d8995-159">Visual Studio 2019 veya sonraki yerlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak için **Varsayılan'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="d8995-160">Proje `<AspNetCoreHostingModel>` özelliği proje dosyasında ayarlarsa, özelliğin`InProcess` değeri `OutOfProcess`(veya ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d8995-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="d8995-161">Özellik yoksa, uygulamanın varsayılan barındırma modeli kullanılır ve bu da işlem aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="d8995-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="d8995-162">Uygulama, uygulamanın normal barındırma modelinden farklı açık bir barındırma modeli ayarı `In Process` `Out Of Process` gerektiriyorsa, **Barındırma Modeli'ni** gerektiğinden biri olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="d8995-163">Profili kaydet.</span><span class="sxs-lookup"><span data-stu-id="d8995-163">Save the profile.</span></span>

<span data-ttu-id="d8995-164">Visual Studio'yu kullanmadığınızda, *Özellikler* klasöründeki [launchSettings.json](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d8995-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="d8995-165">Aşağıdaki örnek, profili HTTPS protokolünü kullanacak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d8995-165">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="d8995-166">Uç `applicationUrl` noktaların `launchUrl` EŞLEŞTİrİlDIĞINI ve HTTP veya HTTPS gibi IIS bağlama yapılandırması ile aynı protokolü kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="d8995-167">Projeyi çalıştırma</span><span class="sxs-lookup"><span data-stu-id="d8995-167">Run the project</span></span>

<span data-ttu-id="d8995-168">Visual Studio'u yönetici olarak çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d8995-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="d8995-169">Yapı yapılandırma açılır listesinin **Hata Ayıklama**olarak ayarlı olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="d8995-170">Hata [Ayıklama Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="d8995-171">Visual Studio yönetici olarak çalışmıyorsa yeniden başlatma isteminde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="d8995-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="d8995-172">İstenirse Visual Studio'yı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d8995-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="d8995-173">Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanızı isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="d8995-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="d8995-174">Bir Sürüm yapı yapılandırması [hata](/visualstudio/debugger/just-my-code) ayıklama Just My Code ve derleyici optimizasyonları ile bozulmuş bir deneyim sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="d8995-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="d8995-175">Örneğin, kesme noktaları vurulmaz.</span><span class="sxs-lookup"><span data-stu-id="d8995-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8995-176">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d8995-176">Additional resources</span></span>

* [<span data-ttu-id="d8995-177">IIS'de IIS Yöneticisi ile Başlarken</span><span class="sxs-lookup"><span data-stu-id="d8995-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8995-178">Bu makalede, Windows Server'da IIS ile çalışan ASP.NET Core uygulamaları hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d8995-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="d8995-179">Bu konu, bu senaryoyu etkinleştirme ve bir proje ayarlama yoluyla yürür.</span><span class="sxs-lookup"><span data-stu-id="d8995-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8995-180">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d8995-180">Prerequisites</span></span>

* [<span data-ttu-id="d8995-181">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8995-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="d8995-182">**ASP.NET ve web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="d8995-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="d8995-183">**.NET Core çapraz platform geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="d8995-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="d8995-184">X.509 güvenlik sertifikası (HTTPS desteği için)</span><span class="sxs-lookup"><span data-stu-id="d8995-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="d8995-185">IIS'yi etkinleştir</span><span class="sxs-lookup"><span data-stu-id="d8995-185">Enable IIS</span></span>

1. <span data-ttu-id="d8995-186">Windows'da **Denetim Masası** > **Programları** > **ve Özellikleri** > **Windows özelliklerini açık veya kapalı** olarak açın (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="d8995-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="d8995-187">Internet **Bilgi Hizmetleri** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="d8995-188">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-188">Select **OK**.</span></span>

<span data-ttu-id="d8995-189">IIS yüklemesi bir sistemin yeniden başlatılmasını gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="d8995-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="d8995-190">IIS hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d8995-190">Configure IIS</span></span>

<span data-ttu-id="d8995-191">IIS'nin aşağıdakilerle yapılandırılmış bir web sitesi olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="d8995-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="d8995-192">**Ana bilgisayar adı** &ndash; Genellikle, **Varsayılan Web Sitesi** Ana Bilgisayar **adı** ile `localhost`kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d8995-192">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="d8995-193">Ancak, benzersiz bir ana bilgisayar adı ile herhangi bir geçerli IIS web sitesi çalışır.</span><span class="sxs-lookup"><span data-stu-id="d8995-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="d8995-194">**Site Bağlama**</span><span class="sxs-lookup"><span data-stu-id="d8995-194">**Site Binding**</span></span>
  * <span data-ttu-id="d8995-195">HTTPS gerektiren uygulamalar için, sertifikalı 443 bağlantı noktasına bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d8995-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="d8995-196">Genellikle, **IIS Express Geliştirme Sertifikası** kullanılır, ancak geçerli sertifika çalışır.</span><span class="sxs-lookup"><span data-stu-id="d8995-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="d8995-197">HTTP kullanan uygulamalar için, 80'i göndermek için bir bağlamanın varlığını onaylayın veya yeni bir site için bağlantı noktası 80'e bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d8995-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="d8995-198">HTTP veya HTTPS için tek bir bağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8995-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="d8995-199">**Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="d8995-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="d8995-200">Visual Studio'da geliştirme zamanı IIS desteğini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="d8995-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="d8995-201">Visual Studio yükleyicisini başlatın.</span><span class="sxs-lookup"><span data-stu-id="d8995-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="d8995-202">IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="d8995-203">ASP.NET **ve web geliştirme** iş yükü için **Geliştirme süresi IIS destek** bileşenini bulun ve yükleyin.</span><span class="sxs-lookup"><span data-stu-id="d8995-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="d8995-204">Bileşen, yükleme **ayrıntıları** panelinde iş yüklerinin sağındaki **Geliştirme süresi IIS desteğiyle** **İsteğe Bağlı** bölümde listelenir.</span><span class="sxs-lookup"><span data-stu-id="d8995-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="d8995-205">Bileşen, IIS ile Core uygulamalarını çalıştırmak için gerekli olan yerel bir IIS modülü olan [ASP.NET Çekirdek Modül'ASP.NET](xref:host-and-deploy/aspnet-core-module)yükler.</span><span class="sxs-lookup"><span data-stu-id="d8995-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="d8995-206">Projeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d8995-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="d8995-207">HTTPS yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="d8995-207">HTTPS redirection</span></span>

<span data-ttu-id="d8995-208">HTTPS gerektiren yeni bir proje için, Yeni bir ASP.NET **Çekirdek Web Uygulaması Oluşturma** penceresinde HTTPS için **Yapılandırmak için** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="d8995-209">Onay kutusunu seçmek, oluşturulduğunda uygulamaya [HTTPS Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) ekler.</span><span class="sxs-lookup"><span data-stu-id="d8995-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="d8995-210">HTTPS gerektiren varolan bir proje için, HTTPS Yeniden Yönlendirme `Startup.Configure`ve HSTS Middleware'i .'da kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8995-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="d8995-211">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="d8995-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="d8995-212">HTTP kullanan bir proje için, [HTTPS Yeniden Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) uygulamaya eklenmez.</span><span class="sxs-lookup"><span data-stu-id="d8995-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="d8995-213">Uygulama yapılandırması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d8995-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="d8995-214">IIS fırlatma profili</span><span class="sxs-lookup"><span data-stu-id="d8995-214">IIS launch profile</span></span>

<span data-ttu-id="d8995-215">Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d8995-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="d8995-216">**Çözüm Gezgini'ndeki**projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="d8995-217">**Özellikleri**seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-217">Select **Properties**.</span></span> <span data-ttu-id="d8995-218">Hata **Ayıklama** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="d8995-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="d8995-219">**Profil**için **Yeni** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="d8995-220">Açılır penceredeki "IIS" profilini adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d8995-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="d8995-221">Profili oluşturmak için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="d8995-222">**Başlatma** ayarı için listeden **IIS'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="d8995-223">**Başlat tarayıcısı** için onay kutusunu seçin ve bitiş noktası URL'sini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="d8995-224">Uygulama HTTPS gerektiriyorsa, bir HTTPS`https://`bitiş noktası ().</span><span class="sxs-lookup"><span data-stu-id="d8995-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="d8995-225">HTTP için bir HTTP`http://`( ) bitiş noktası kullanın.</span><span class="sxs-lookup"><span data-stu-id="d8995-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="d8995-226">Önceki kullanımları belirtilen [IIS yapılandırması](#configure-iis)ile aynı ana `localhost`bilgisayar adı ve bağlantı noktasını sağlayın , genellikle .</span><span class="sxs-lookup"><span data-stu-id="d8995-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="d8995-227">URL'nin sonunda uygulamanın adını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="d8995-228">Örneğin, `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` veya (HTTP) geçerli uç nokta URL'leridir.</span><span class="sxs-lookup"><span data-stu-id="d8995-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="d8995-229">Çevre **değişkenleri** bölümünde **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="d8995-230">**Bir Ad** `ASPNETCORE_ENVIRONMENT` ve **Değeri** olan bir `Development`ortam değişkeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="d8995-231">Web **Sunucusu Ayarları** alanında, **Uygulama URL'sini** **Başlat tarayıcıuç** noktası URL'si için kullanılan değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="d8995-232">Visual Studio 2019 veya sonraki yerlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak için **Varsayılan'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="d8995-233">Proje `<AspNetCoreHostingModel>` özelliği proje dosyasında ayarlarsa, özelliğin`InProcess` değeri `OutOfProcess`(veya ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d8995-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="d8995-234">Özellik yoksa, uygulamanın varsayılan barındırma modeli kullanılır ve bu da işlem dışıdır.</span><span class="sxs-lookup"><span data-stu-id="d8995-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="d8995-235">Uygulama, uygulamanın normal barındırma modelinden farklı açık bir barındırma modeli ayarı `In Process` `Out Of Process` gerektiriyorsa, **Barındırma Modeli'ni** gerektiğinden biri olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="d8995-236">Profili kaydet.</span><span class="sxs-lookup"><span data-stu-id="d8995-236">Save the profile.</span></span>

<span data-ttu-id="d8995-237">Visual Studio'yu kullanmadığınızda, *Özellikler* klasöründeki [launchSettings.json](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d8995-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="d8995-238">Aşağıdaki örnek, profili HTTPS protokolünü kullanacak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d8995-238">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="d8995-239">Uç `applicationUrl` noktaların `launchUrl` EŞLEŞTİrİlDIĞINI ve HTTP veya HTTPS gibi IIS bağlama yapılandırması ile aynı protokolü kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="d8995-240">Projeyi çalıştırma</span><span class="sxs-lookup"><span data-stu-id="d8995-240">Run the project</span></span>

<span data-ttu-id="d8995-241">Visual Studio'u yönetici olarak çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d8995-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="d8995-242">Yapı yapılandırma açılır listesinin **Hata Ayıklama**olarak ayarlı olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d8995-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="d8995-243">Hata [Ayıklama Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="d8995-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="d8995-244">Visual Studio yönetici olarak çalışmıyorsa yeniden başlatma isteminde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="d8995-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="d8995-245">İstenirse Visual Studio'yı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d8995-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="d8995-246">Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanızı isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="d8995-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="d8995-247">Bir Sürüm yapı yapılandırması [hata](/visualstudio/debugger/just-my-code) ayıklama Just My Code ve derleyici optimizasyonları ile bozulmuş bir deneyim sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="d8995-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="d8995-248">Örneğin, kesme noktaları vurulmaz.</span><span class="sxs-lookup"><span data-stu-id="d8995-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8995-249">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d8995-249">Additional resources</span></span>

* [<span data-ttu-id="d8995-250">IIS'de IIS Yöneticisi ile Başlarken</span><span class="sxs-lookup"><span data-stu-id="d8995-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
