---
title: ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği
author: rick-anderson
description: Windows Server 'da IIS ile çalışırken ASP.NET Core uygulamalarda hata ayıklama desteğini bulur.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ba4d47e0500d7f3287e6a57d10036d774c4b19f4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015671"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="f44af-103">ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği</span><span class="sxs-lookup"><span data-stu-id="f44af-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="f44af-104">, [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="f44af-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f44af-105">Bu makalede, Windows Server 'da IIS ile çalışan ASP.NET Core hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44af-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="f44af-106">Bu konu başlığı altında, bu senaryonun etkinleştirilmesi ve bir projenin kurulması anlatılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44af-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f44af-107">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="f44af-107">Prerequisites</span></span>

* [<span data-ttu-id="f44af-108">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f44af-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="f44af-109">**ASP.net ve Web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="f44af-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="f44af-110">**.NET Core platformlar arası geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="f44af-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="f44af-111">X. 509.440 güvenlik sertifikası (HTTPS desteği için)</span><span class="sxs-lookup"><span data-stu-id="f44af-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="f44af-112">IIS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f44af-112">Enable IIS</span></span>

1. <span data-ttu-id="f44af-113">Windows 'ta, **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafı) bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f44af-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="f44af-114">**Internet Information Services** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="f44af-115">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-115">Select **OK**.</span></span>

<span data-ttu-id="f44af-116">IIS yüklemesi için sistemin yeniden başlatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f44af-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="f44af-117">IIS hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f44af-117">Configure IIS</span></span>

<span data-ttu-id="f44af-118">IIS 'nin aşağıdaki ile yapılandırılmış bir Web sitesine sahip olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="f44af-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="f44af-119">**Ana bilgisayar adı**: genellikle **varsayılan Web sitesi** , bir **ana bilgisayar adıyla** kullanılır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f44af-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="f44af-120">Ancak, benzersiz bir ana bilgisayar adına sahip geçerli bir IIS Web sitesi çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44af-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="f44af-121">**Site bağlama**</span><span class="sxs-lookup"><span data-stu-id="f44af-121">**Site Binding**</span></span>
  * <span data-ttu-id="f44af-122">HTTPS gerektiren uygulamalar için, sertifika ile 443 numaralı bağlantı noktasına bir bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f44af-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="f44af-123">Genellikle **IIS Express geliştirme sertifikası** kullanılır, ancak geçerli bir sertifika çalışıyor olur.</span><span class="sxs-lookup"><span data-stu-id="f44af-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="f44af-124">HTTP kullanan uygulamalar için, 80 veya yeni bir site için bağlantı noktası 80 ' e bir bağlama oluşturmak için bir bağlamanın mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="f44af-125">HTTP veya HTTPS için tek bir bağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="f44af-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="f44af-126">**Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="f44af-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="f44af-127">Visual Studio 'da geliştirme zamanı IIS desteğini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f44af-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="f44af-128">Visual Studio yükleyicisi 'ni başlatın.</span><span class="sxs-lookup"><span data-stu-id="f44af-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="f44af-129">IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="f44af-130">**ASP.net ve Web geliştirme** iş yükü için **geliştirme zamanı IIS destek** bileşeni ' ni bulun ve yükler.</span><span class="sxs-lookup"><span data-stu-id="f44af-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="f44af-131">Bileşen, iş yüklerinin sağındaki **Yükleme ayrıntıları** panelinde, **geliştirme zamanı IIS desteği** altındaki **isteğe bağlı** bölümde listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f44af-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="f44af-132">Bileşen, IIS ile ASP.NET Core uygulamaları çalıştırmak için gerekli yerel bir IIS modülü olan [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="f44af-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="f44af-133">Projeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f44af-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="f44af-134">HTTPS yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="f44af-134">HTTPS redirection</span></span>

<span data-ttu-id="f44af-135">HTTPS gerektiren yeni bir proje için **yeni ASP.NET Core Web uygulaması oluşturma** penceresinde **https için yapılandırmak** üzere onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="f44af-136">Onay kutusunun belirlenmesi, uygulama oluşturulduğunda [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) ekler.</span><span class="sxs-lookup"><span data-stu-id="f44af-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="f44af-137">HTTPS gerektiren mevcut bir proje için ' de HTTPS yeniden yönlendirme ve HSTS ara yazılımı kullanın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f44af-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="f44af-138">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="f44af-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="f44af-139">HTTP kullanan bir proje için, [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) uygulamaya eklenmez.</span><span class="sxs-lookup"><span data-stu-id="f44af-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="f44af-140">Uygulama yapılandırması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f44af-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="f44af-141">IIS başlatma profili</span><span class="sxs-lookup"><span data-stu-id="f44af-141">IIS launch profile</span></span>

<span data-ttu-id="f44af-142">Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f44af-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="f44af-143">**Çözüm Gezgini**projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="f44af-144">**Özellikler**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-144">Select **Properties**.</span></span> <span data-ttu-id="f44af-145">**Hata Ayıkla** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="f44af-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="f44af-146">**Profil**için **Yeni** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="f44af-147">Profili, açılan pencerede "IIS" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="f44af-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="f44af-148">Profili oluşturmak için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="f44af-149">**Başlatma** ayarı Için listeden **IIS** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="f44af-150">**Başlat tarayıcısı** onay kutusunu seçin ve uç nokta URL 'sini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="f44af-151">Uygulama HTTPS gerektirdiğinde, bir HTTPS uç noktası () kullanın `https://` .</span><span class="sxs-lookup"><span data-stu-id="f44af-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="f44af-152">HTTP için bir HTTP ( `http://` ) uç noktası kullanın.</span><span class="sxs-lookup"><span data-stu-id="f44af-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="f44af-153">[Daha önce belirtilen IIS yapılandırmasıyla](#configure-iis)aynı ana bilgisayar adını ve bağlantı noktasını (genellikle) sağlayın `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f44af-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="f44af-154">URL 'nin sonundaki uygulamanın adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="f44af-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="f44af-155">Örneğin, `https://localhost/WebApplication1` (https) veya `http://localhost/WebApplication1` (http) geçerli uç nokta URL 'lardır.</span><span class="sxs-lookup"><span data-stu-id="f44af-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="f44af-156">**Ortam değişkenleri** bölümünde **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="f44af-157">**Adı** ve değeri olan bir ortam değişkeni sağlayın `ASPNETCORE_ENVIRONMENT` **Value** `Development` .</span><span class="sxs-lookup"><span data-stu-id="f44af-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="f44af-158">**Web sunucusu ayarları** alanında, **Uygulama URL** 'sini **başlatma tarayıcısı** uç noktası URL 'si için kullanılan aynı değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="f44af-159">Visual Studio 2019 veya sonraki sürümlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak üzere **varsayılan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="f44af-160">Proje, `<AspNetCoreHostingModel>` Proje dosyasında özelliği ayarlarsa, özelliğin değeri ( `InProcess` veya `OutOfProcess` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f44af-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="f44af-161">Özellik mevcut değilse, uygulamanın varsayılan barındırma modeli, işlem içi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f44af-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="f44af-162">Uygulama, uygulamanın normal barındırma modelinden farklı bir açık barındırma modeli ayarı gerektiriyorsa, **barındırma modelini** gereken ya da gerektiği şekilde ayarlayın `In Process` `Out Of Process` .</span><span class="sxs-lookup"><span data-stu-id="f44af-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="f44af-163">Profili kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f44af-163">Save the profile.</span></span>

<span data-ttu-id="f44af-164">Visual Studio kullanmadığınız durumlarda, *Özellikler* klasöründeki [launchSettings.js](https://json.schemastore.org/launchsettings) dosya için el ile bir başlatma profili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f44af-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="f44af-165">Aşağıdaki örnek, HTTPS protokolünü kullanmak için profili yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f44af-165">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="f44af-166">`applicationUrl`Ve `launchUrl` bitiş noktalarının EŞLEŞTIĞINI ve IIS bağlama yapılandırmasıyla aynı Protokolü (http veya https) kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="f44af-167">Projeyi çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f44af-167">Run the project</span></span>

<span data-ttu-id="f44af-168">Visual Studio 'Yu yönetici olarak çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f44af-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="f44af-169">Derleme yapılandırması açılır listesinin **hata ayıklama**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="f44af-170">[Hata ayıklamayı Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="f44af-171">Visual Studio, yönetici olarak çalışmıyorsa bir yeniden başlatma isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="f44af-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="f44af-172">İstenirse, Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f44af-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="f44af-173">Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f44af-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="f44af-174">[Yalnızca kendi kodum](/visualstudio/debugger/just-my-code) ve derleyici Iyileştirmeleriyle yayın derleme yapılandırması hata ayıklaması, düzeyi düşürülmüş bir deneyimle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="f44af-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="f44af-175">Örneğin, kesme noktaları isabet edilmez.</span><span class="sxs-lookup"><span data-stu-id="f44af-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f44af-176">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f44af-176">Additional resources</span></span>

* [<span data-ttu-id="f44af-177">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="f44af-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f44af-178">Bu makalede, Windows Server 'da IIS ile çalışan ASP.NET Core hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44af-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="f44af-179">Bu konu başlığı altında, bu senaryonun etkinleştirilmesi ve bir projenin kurulması anlatılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44af-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f44af-180">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="f44af-180">Prerequisites</span></span>

* [<span data-ttu-id="f44af-181">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f44af-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="f44af-182">**ASP.net ve Web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="f44af-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="f44af-183">**.NET Core platformlar arası geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="f44af-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="f44af-184">X. 509.440 güvenlik sertifikası (HTTPS desteği için)</span><span class="sxs-lookup"><span data-stu-id="f44af-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="f44af-185">IIS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f44af-185">Enable IIS</span></span>

1. <span data-ttu-id="f44af-186">Windows 'ta, **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafı) bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f44af-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="f44af-187">**Internet Information Services** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="f44af-188">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-188">Select **OK**.</span></span>

<span data-ttu-id="f44af-189">IIS yüklemesi için sistemin yeniden başlatılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f44af-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="f44af-190">IIS hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f44af-190">Configure IIS</span></span>

<span data-ttu-id="f44af-191">IIS 'nin aşağıdaki ile yapılandırılmış bir Web sitesine sahip olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="f44af-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="f44af-192">**Ana bilgisayar adı**: genellikle **varsayılan Web sitesi** , bir **ana bilgisayar adıyla** kullanılır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f44af-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="f44af-193">Ancak, benzersiz bir ana bilgisayar adına sahip geçerli bir IIS Web sitesi çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44af-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="f44af-194">**Site bağlama**</span><span class="sxs-lookup"><span data-stu-id="f44af-194">**Site Binding**</span></span>
  * <span data-ttu-id="f44af-195">HTTPS gerektiren uygulamalar için, sertifika ile 443 numaralı bağlantı noktasına bir bağlama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f44af-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="f44af-196">Genellikle **IIS Express geliştirme sertifikası** kullanılır, ancak geçerli bir sertifika çalışıyor olur.</span><span class="sxs-lookup"><span data-stu-id="f44af-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="f44af-197">HTTP kullanan uygulamalar için, 80 veya yeni bir site için bağlantı noktası 80 ' e bir bağlama oluşturmak için bir bağlamanın mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="f44af-198">HTTP veya HTTPS için tek bir bağlama kullanın.</span><span class="sxs-lookup"><span data-stu-id="f44af-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="f44af-199">**Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="f44af-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="f44af-200">Visual Studio 'da geliştirme zamanı IIS desteğini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f44af-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="f44af-201">Visual Studio yükleyicisi 'ni başlatın.</span><span class="sxs-lookup"><span data-stu-id="f44af-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="f44af-202">IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="f44af-203">**ASP.net ve Web geliştirme** iş yükü için **geliştirme zamanı IIS destek** bileşeni ' ni bulun ve yükler.</span><span class="sxs-lookup"><span data-stu-id="f44af-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="f44af-204">Bileşen, iş yüklerinin sağındaki **Yükleme ayrıntıları** panelinde, **geliştirme zamanı IIS desteği** altındaki **isteğe bağlı** bölümde listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f44af-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="f44af-205">Bileşen, IIS ile ASP.NET Core uygulamaları çalıştırmak için gerekli yerel bir IIS modülü olan [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="f44af-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="f44af-206">Projeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f44af-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="f44af-207">HTTPS yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="f44af-207">HTTPS redirection</span></span>

<span data-ttu-id="f44af-208">HTTPS gerektiren yeni bir proje için **yeni ASP.NET Core Web uygulaması oluşturma** penceresinde **https için yapılandırmak** üzere onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="f44af-209">Onay kutusunun belirlenmesi, uygulama oluşturulduğunda [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) ekler.</span><span class="sxs-lookup"><span data-stu-id="f44af-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="f44af-210">HTTPS gerektiren mevcut bir proje için ' de HTTPS yeniden yönlendirme ve HSTS ara yazılımı kullanın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f44af-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="f44af-211">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="f44af-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="f44af-212">HTTP kullanan bir proje için, [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) uygulamaya eklenmez.</span><span class="sxs-lookup"><span data-stu-id="f44af-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="f44af-213">Uygulama yapılandırması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f44af-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="f44af-214">IIS başlatma profili</span><span class="sxs-lookup"><span data-stu-id="f44af-214">IIS launch profile</span></span>

<span data-ttu-id="f44af-215">Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f44af-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="f44af-216">**Çözüm Gezgini**projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="f44af-217">**Özellikler**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-217">Select **Properties**.</span></span> <span data-ttu-id="f44af-218">**Hata Ayıkla** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="f44af-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="f44af-219">**Profil**için **Yeni** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="f44af-220">Profili, açılan pencerede "IIS" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="f44af-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="f44af-221">Profili oluşturmak için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="f44af-222">**Başlatma** ayarı Için listeden **IIS** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="f44af-223">**Başlat tarayıcısı** onay kutusunu seçin ve uç nokta URL 'sini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="f44af-224">Uygulama HTTPS gerektirdiğinde, bir HTTPS uç noktası () kullanın `https://` .</span><span class="sxs-lookup"><span data-stu-id="f44af-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="f44af-225">HTTP için bir HTTP ( `http://` ) uç noktası kullanın.</span><span class="sxs-lookup"><span data-stu-id="f44af-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="f44af-226">[Daha önce belirtilen IIS yapılandırmasıyla](#configure-iis)aynı ana bilgisayar adını ve bağlantı noktasını (genellikle) sağlayın `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f44af-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="f44af-227">URL 'nin sonundaki uygulamanın adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="f44af-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="f44af-228">Örneğin, `https://localhost/WebApplication1` (https) veya `http://localhost/WebApplication1` (http) geçerli uç nokta URL 'lardır.</span><span class="sxs-lookup"><span data-stu-id="f44af-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="f44af-229">**Ortam değişkenleri** bölümünde **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="f44af-230">**Adı** ve değeri olan bir ortam değişkeni sağlayın `ASPNETCORE_ENVIRONMENT` **Value** `Development` .</span><span class="sxs-lookup"><span data-stu-id="f44af-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="f44af-231">**Web sunucusu ayarları** alanında, **Uygulama URL** 'sini **başlatma tarayıcısı** uç noktası URL 'si için kullanılan aynı değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="f44af-232">Visual Studio 2019 veya sonraki sürümlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak üzere **varsayılan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="f44af-233">Proje, `<AspNetCoreHostingModel>` Proje dosyasında özelliği ayarlarsa, özelliğin değeri ( `InProcess` veya `OutOfProcess` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f44af-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="f44af-234">Özellik mevcut değilse, uygulamanın varsayılan barındırma modeli kullanılır ve bu işlem, işlem dışı olur.</span><span class="sxs-lookup"><span data-stu-id="f44af-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="f44af-235">Uygulama, uygulamanın normal barındırma modelinden farklı bir açık barındırma modeli ayarı gerektiriyorsa, **barındırma modelini** gereken ya da gerektiği şekilde ayarlayın `In Process` `Out Of Process` .</span><span class="sxs-lookup"><span data-stu-id="f44af-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="f44af-236">Profili kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f44af-236">Save the profile.</span></span>

<span data-ttu-id="f44af-237">Visual Studio kullanmadığınız durumlarda, *Özellikler* klasöründeki [launchSettings.js](https://json.schemastore.org/launchsettings) dosya için el ile bir başlatma profili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f44af-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="f44af-238">Aşağıdaki örnek, HTTPS protokolünü kullanmak için profili yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f44af-238">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="f44af-239">`applicationUrl`Ve `launchUrl` bitiş noktalarının EŞLEŞTIĞINI ve IIS bağlama yapılandırmasıyla aynı Protokolü (http veya https) kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="f44af-240">Projeyi çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f44af-240">Run the project</span></span>

<span data-ttu-id="f44af-241">Visual Studio 'Yu yönetici olarak çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f44af-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="f44af-242">Derleme yapılandırması açılır listesinin **hata ayıklama**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f44af-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="f44af-243">[Hata ayıklamayı Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="f44af-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="f44af-244">Visual Studio, yönetici olarak çalışmıyorsa bir yeniden başlatma isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="f44af-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="f44af-245">İstenirse, Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f44af-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="f44af-246">Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f44af-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="f44af-247">[Yalnızca kendi kodum](/visualstudio/debugger/just-my-code) ve derleyici Iyileştirmeleriyle yayın derleme yapılandırması hata ayıklaması, düzeyi düşürülmüş bir deneyimle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="f44af-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="f44af-248">Örneğin, kesme noktaları isabet edilmez.</span><span class="sxs-lookup"><span data-stu-id="f44af-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f44af-249">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f44af-249">Additional resources</span></span>

* [<span data-ttu-id="f44af-250">IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="f44af-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
