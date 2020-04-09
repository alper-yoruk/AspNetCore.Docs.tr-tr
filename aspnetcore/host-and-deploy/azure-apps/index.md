---
title: Temel uygulamaları Azure Uygulama Hizmetine ASP.NET dağıtma
author: bradygaster
description: Bu makalede, Azure ana bilgisayar ve dağıtım kaynakları bağlantıları içerir.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665146"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="6bbe2-103">Temel uygulamaları Azure Uygulama Hizmetine ASP.NET dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="6bbe2-104">[Azure App Service,](https://azure.microsoft.com/services/app-service/) ASP.NET Core dahil olmak üzere web uygulamalarını barındırmak için bir [Microsoft bulut bilgi işlem platformu hizmetidir.](https://azure.microsoft.com/)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="6bbe2-105">Yararlı kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6bbe2-105">Useful resources</span></span>

<span data-ttu-id="6bbe2-106">[Uygulama Hizmeti Dokümantasyonu,](/azure/app-service/) Azure Apps dokümantasyon, öğreticiler, örnekler, nasıl yapılır kılavuzları ve diğer kaynakların yuvasıdır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="6bbe2-107">ASP.NET Core uygulamaları barındırma ile ilgili iki önemli öğreticiler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="6bbe2-108">Azure’da ASP.NET Core web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="6bbe2-109">Windows'daki Azure Uygulama Hizmeti'ne ASP.NET Bir Çekirdek web uygulaması oluşturmak ve dağıtmak için Visual Studio'yı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="6bbe2-110">Linux'ta Uygulama Hizmeti'nde ASP.NET Core uygulaması oluşturun</span><span class="sxs-lookup"><span data-stu-id="6bbe2-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="6bbe2-111">Linux'ta Azure Uygulama Hizmeti'ne bir ASP.NET Çekirdek web uygulaması oluşturmak ve dağıtmak için komut satırını kullanın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="6bbe2-112">Azure App hizmetinde bulunan ASP.NET Core sürümü için [App Service Dashboard'daki ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="6bbe2-113">[Uygulama Hizmeti Duyuruları](https://github.com/Azure/app-service-announcements/) deposuna abone olun ve sorunları izleyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="6bbe2-114">Uygulama Hizmeti ekibi, Uygulama Hizmeti'ne gelen duyuruları ve senaryoları düzenli olarak yayınlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="6bbe2-115">Aşağıdaki makaleler ASP.NET Temel belgelerde mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="6bbe2-116">Visual Studio'ASP.NET bir ASP.NET Core uygulamasını Azure Uygulama Hizmeti'nde nasıl yayınlayacağınızı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="6bbe2-117">Visual Studio'yu kullanarak bir ASP.NET Core web uygulaması oluşturmayı ve sürekli dağıtım için Git'i kullanarak Azure Uygulama Hizmeti'ne nasıl dağıttığı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="6bbe2-118">İlk işlem hattınızı oluşturma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="6bbe2-119">ASP.NET Core uygulaması için bir CI yapısı ayarlayın ve ardından Azure Uygulama Hizmeti'ne sürekli dağıtım sürümü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="6bbe2-120">Azure Web App sandbox</span><span class="sxs-lookup"><span data-stu-id="6bbe2-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="6bbe2-121">Azure Apps platformu tarafından uygulanan Azure Uygulama Hizmeti çalışma zamanı yürütme sınırlamalarını keşfedin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="6bbe2-122">ASP.NET Core projeleri ile uyarıları ve hataları anlayın ve sorun giderin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="6bbe2-123">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6bbe2-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="6bbe2-124">Platform</span><span class="sxs-lookup"><span data-stu-id="6bbe2-124">Platform</span></span>

<span data-ttu-id="6bbe2-125">Bir Uygulama Hizmetleri uygulamasının platform mimarisi (x86/x64), A serisi bilgi işlem (Temel) veya daha yüksek barındırma katmanında barındırılan uygulamalar için Uygulamanın Azure Portalı'ndaki ayarlarında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="6bbe2-126">Uygulamanın yayımlama ayarlarının (örneğin, Visual Studio [yayımlama profilinde (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) Uygulamanın Azure Portalı'ndaki hizmet yapılandırmasındaki ayarla eşleşin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6bbe2-127">Azure Uygulama Hizmeti'nde 64 bit (x64) ve 32 bit (x86) uygulamaların çalışma süreleri mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="6bbe2-128">App Service'de kullanılabilen [.NET Core SDK](/dotnet/core/sdk) 32 bittir, ancak [Kudu](https://github.com/projectkudu/kudu/wiki) konsolu veya Visual Studio'da yayımlama işlemini kullanarak yerel olarak oluşturulmuş 64 bit uygulamaları dağıtabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="6bbe2-129">Daha fazla bilgi için [Yayımla bölümüne](#publish-and-deploy-the-app) bakın ve uygulama bölümünü dağıtın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6bbe2-130">Yerel bağımlılıklara sahip uygulamalar için Azure Uygulama Hizmeti'nde 32 bit (x86) uygulamaların çalışma saatleri bulunur.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="6bbe2-131">App Service'de bulunan [.NET Core SDK](/dotnet/core/sdk) 32 bit'tir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="6bbe2-132">.NET Core çalışma zamanı ve .NET Core SDK gibi .NET Çekirdek çerçeve bileşenleri ve dağıtım yöntemleri hakkında daha fazla bilgi için [bkz.](/dotnet/core/about#composition)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="6bbe2-133">Paketler</span><span class="sxs-lookup"><span data-stu-id="6bbe2-133">Packages</span></span>

<span data-ttu-id="6bbe2-134">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için otomatik günlüğe kaydetme özellikleri sağlamak için aşağıdaki NuGet paketlerini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="6bbe2-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup,](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) Azure Uygulama Hizmeti ile ASP.NET Temel ışık-up tümleştirmesağlamak için [IHostingStartup'ı](xref:fundamentals/configuration/platform-specific-configuration) kullanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="6bbe2-136">Eklenen günlük özellikleri `Microsoft.AspNetCore.AzureAppServicesIntegration` paket tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="6bbe2-137">[Microsoft.AspNetCore.AzureAppServicesIntegration,](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) Pakete Azure App Service tanılama günlük sağlayıcıları eklemek için [AddAzureWebAppDiagnostics'i yürütür.](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) `Microsoft.Extensions.Logging.AzureAppServices`</span><span class="sxs-lookup"><span data-stu-id="6bbe2-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="6bbe2-138">[Microsoft.Extensions.Logging.AzureAppServices,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) Azure App Service tanılama günlüklerini ve günlük akış özelliklerini desteklemek için logger uygulamaları sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="6bbe2-139">Önceki paketler [Microsoft.AspNetCore.App metapaketinden](xref:fundamentals/metapackage-app)kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="6bbe2-140">.NET Framework'u hedefleyen `Microsoft.AspNetCore.App` veya meta pakete başvuran uygulamaların, uygulamanın proje dosyasındaki tek tek paketlere açıkça başvurması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="6bbe2-141">Azure Portalı'nı kullanarak uygulama yapılandırmasını geçersiz kılın</span><span class="sxs-lookup"><span data-stu-id="6bbe2-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="6bbe2-142">Azure Portalı'ndaki uygulama ayarları, uygulama için ortam değişkenleri ayarlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="6bbe2-143">Çevre değişkenleri [Çevre Değişkenleri Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider)tarafından tüketilebilir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="6bbe2-144">Azure Portalı'nda bir uygulama ayarı oluşturulduğunda veya değiştirildiğinde ve **Kaydet** düğmesi seçildiğinde, Azure Uygulaması yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="6bbe2-145">Ortam değişkeni, hizmet yeniden başlatıldıktan sonra uygulama için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6bbe2-146">Bir uygulama [Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullandığında, ortam değişkenleri <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ana bilgisayarı oluşturmak için çağrıldığında uygulamanın yapılandırmasına yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="6bbe2-147">Daha fazla bilgi <xref:fundamentals/host/generic-host> için bkz ve [Çevre Değişkenleri Yapılandırma Sağlayıcısı.](xref:fundamentals/configuration/index#environment-variables-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6bbe2-148">Bir uygulama [Web Host'u](xref:fundamentals/host/web-host)kullandığında, ortam değişkenleri ana <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bilgisayarı oluşturmak için çağrıldığında uygulamanın yapılandırmasına yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="6bbe2-149">Daha fazla bilgi <xref:fundamentals/host/web-host> için bkz ve [Çevre Değişkenleri Yapılandırma Sağlayıcısı.](xref:fundamentals/configuration/index#environment-variables-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6bbe2-150">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="6bbe2-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6bbe2-151">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma yaparken İleriye Dönük Üstbilgileri Ara ware'i yapılandıran [IIS Tümleştirme Aracı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)ve ASP.NET Çekirdek Modülü, düzeni (HTTP/HTTPS) ve isteğin kaynağı olduğu uzak IP adresini iletecek şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="6bbe2-152">Ek proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="6bbe2-153">Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="6bbe2-154">İzleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="6bbe2-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6bbe2-155">ASP.NET App Service'e dağıtılan Core uygulamaları otomatik olarak bir Uygulama Hizmeti uzantısı alır, **ASP.NET Çekirdek Günlük Tümleştirmesi.**</span><span class="sxs-lookup"><span data-stu-id="6bbe2-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="6bbe2-156">Uzantı, Azure Uygulama Hizmeti'ndeki ASP.NET Core uygulamaları için günlük tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6bbe2-157">ASP.NET Uygulama Hizmetine dağıtılan Core uygulamaları otomatik olarak bir Uygulama Hizmeti uzantısı alır, **ASP.NET Core Günlük Uzantıları.**</span><span class="sxs-lookup"><span data-stu-id="6bbe2-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="6bbe2-158">Uzantı, Azure Uygulama Hizmeti'ndeki ASP.NET Core uygulamaları için günlük tümleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="6bbe2-159">Bilgileri izlemek, günlüğe kaydetme ve sorun giderme için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="6bbe2-160">Azure Uygulama Hizmeti'ndeki uygulamaları izleme</span><span class="sxs-lookup"><span data-stu-id="6bbe2-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="6bbe2-161">Uygulamalar ve Uygulama Hizmeti planları için kotaları ve ölçümleri nasıl inceleyiş olarak değerlendirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="6bbe2-162">Azure Uygulama Hizmeti'ndeki uygulamalar için tanılama günlüğe kaydetmeyi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="6bbe2-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="6bbe2-163">HTTP durum kodları, başarısız istekler ve web sunucusu etkinliği için tanıgünlüğe nasıl erişileni ve etkinleştirmeyi keşfedin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="6bbe2-164">ASP.NET Core uygulamalarında işleme hatalarına yönelik yaygın yaklaşımları anlayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="6bbe2-165">Azure Uygulama Hizmeti dağıtımlarıyla ilgili sorunları ASP.NET Core uygulamalarıyla nasıl tanılayın öğrenin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="6bbe2-166">Sorun giderme önerileriyle Azure App Service/IIS tarafından barındırılan uygulamalar için yaygın dağıtım yapılandırma hatalarını görün.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="6bbe2-167">Veri Koruma anahtarlık ve dağıtım yuvaları</span><span class="sxs-lookup"><span data-stu-id="6bbe2-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="6bbe2-168">[Veri Koruma anahtarları](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) *%HOME%\ASP.NET\DataProtection-Keys* klasörüne kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="6bbe2-169">Bu klasör ağ depolama tarafından desteklenen ve uygulamayı barındıran tüm makineler arasında senkronize edilir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="6bbe2-170">Anahtarlar istirahatte korunmuyor.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="6bbe2-171">Bu klasör, tek bir dağıtım yuvasındaki bir uygulamanın tüm örneklerine anahtarlık sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="6bbe2-172">Hazırlama ve Üretim gibi ayrı dağıtım yuvaları bir anahtarlık paylaşmaz.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="6bbe2-173">Dağıtım yuvaları arasında geçiş yaparken, veri koruması kullanan herhangi bir sistem, önceki yuvadaki anahtarlık kullanarak depolanan verilerin şifresini çözemez.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="6bbe2-174">ASP.NET Çerez Middleware çerezleri korumak için veri koruması kullanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="6bbe2-175">Bu, kullanıcıların Çerez Ara Ware'ASP.NET standartını kullanan bir uygulamanın dışında oturum alabilmelerine yol açar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="6bbe2-176">Yuvadan bağımsız bir anahtarlık çözümü için, şu gibi harici bir anahtarlık sağlayıcısı kullanın:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="6bbe2-177">Azure Blob Depolama</span><span class="sxs-lookup"><span data-stu-id="6bbe2-177">Azure Blob Storage</span></span>
* <span data-ttu-id="6bbe2-178">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6bbe2-178">Azure Key Vault</span></span>
* <span data-ttu-id="6bbe2-179">SQL deposu</span><span class="sxs-lookup"><span data-stu-id="6bbe2-179">SQL store</span></span>
* <span data-ttu-id="6bbe2-180">Redis önbelleği</span><span class="sxs-lookup"><span data-stu-id="6bbe2-180">Redis cache</span></span>

<span data-ttu-id="6bbe2-181">Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="6bbe2-182">.NET Core önizlemesini kullanan bir ASP.NET Core uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="6bbe2-183">.NET Core'un önizleme sürümü kullanan bir uygulamayı dağıtmak için aşağıdaki kaynaklara bakın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="6bbe2-184">Bu yaklaşımlar, çalışma zamanı kullanılabilir olduğunda ancak SDK Azure Uygulama Hizmeti'ne yüklenmediği zaman da kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="6bbe2-185">Azure Boru Hatlarını kullanarak .NET Core SDK Sürümünü belirtin</span><span class="sxs-lookup"><span data-stu-id="6bbe2-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="6bbe2-186">Bağımsız bir önizleme uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="6bbe2-187">Kapsayıcılar için Web Uygulamaları ile Docker'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="6bbe2-188">Önizleme sitesi uzantısını yükleme</span><span class="sxs-lookup"><span data-stu-id="6bbe2-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="6bbe2-189">Azure App hizmetinde bulunan ASP.NET Core sürümü için [App Service Dashboard'daki ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="6bbe2-190">Azure Boru Hatlarını kullanarak .NET Core SDK Sürümünü belirtin</span><span class="sxs-lookup"><span data-stu-id="6bbe2-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="6bbe2-191">Azure DevOps ile sürekli bir tümleştirme yapısı oluşturmak için [Azure App Service CI/CD senaryolarını](/azure/app-service/deploy-continuous-deployment) kullanın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="6bbe2-192">Azure DevOps yapısı oluşturulduktan sonra, isteğe bağlı olarak yapıyı belirli bir SDK sürümünü kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="6bbe2-193">.NET Core SDK sürümünü belirtin</span><span class="sxs-lookup"><span data-stu-id="6bbe2-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="6bbe2-194">Bir Azure DevOps oluşturmak için Uygulama Hizmeti dağıtım merkezini kullanırken, `Restore` `Build`varsayılan `Test`yapı `Publish`ardışık yapısı , , , ve .</span><span class="sxs-lookup"><span data-stu-id="6bbe2-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="6bbe2-195">SDK sürümünü belirtmek için, yeni bir adım eklemek için Aracı iş listesindeki **Ekle (+)** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="6bbe2-196">Arama çubuğunda **.NET Core SDK'yı** arayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![.NET Core SDK adımını ekleyin](index/add-sdk-step.png)

<span data-ttu-id="6bbe2-198">Adımı,.NET Core SDK'nın belirtilen sürümünü kullanabilmek için yapıdaki ilk konuma taşıyın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="6bbe2-199">.NET Core SDK sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="6bbe2-200">Bu örnekte, SDK `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-200">In this example, the SDK is set to `3.0.100`.</span></span>

![Tamamlanan SDK adımı](index/sdk-step-first-place.png)

<span data-ttu-id="6bbe2-202">Bağımsız bir [dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)yayımlamak için, adımda SCD'yi `Publish` yapılandırın ve [Runtime Tanımlayıcısı'nı (RID)](/dotnet/core/rid-catalog)sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Bağımsız yayımlama](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="6bbe2-204">Bağımsız bir önizleme uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="6bbe2-205">Önizleme çalışma zamanını hedefleyen bağımsız bir [dağıtım (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd) dağıtımda önizleme çalışma zamanını taşır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="6bbe2-206">Bağımsız bir uygulama dağıtılırken:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="6bbe2-207">Azure Uygulama Hizmeti'ndeki site [önizleme sitesi uzantısı](#install-the-preview-site-extension)gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="6bbe2-208">Uygulama, [çerçeveye bağımlı bir dağıtım (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd)için yayımlama sırasından farklı bir yaklaşımla yayımlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="6bbe2-209">[Uygulamayı kendi kendine yeten](#deploy-the-app-self-contained) bölüme dağıt'taki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="6bbe2-210">Kapsayıcılar için Web Uygulamaları ile Docker'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="6bbe2-211">[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) en son önizleme Docker görüntülerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="6bbe2-212">Görüntüler temel görüntü olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-212">The images can be used as a base image.</span></span> <span data-ttu-id="6bbe2-213">Resmi kullanın ve kapsayıcılar için Web Apps'a normal olarak dağıtın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="6bbe2-214">Önizleme sitesi uzantısını yükleme</span><span class="sxs-lookup"><span data-stu-id="6bbe2-214">Install the preview site extension</span></span>

<span data-ttu-id="6bbe2-215">Önizleme sitesi uzantısını kullanarak bir sorun oluşursa, [bir dotnet/AspNetCore sorunu](https://github.com/dotnet/AspNetCore/issues)açın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-215">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="6bbe2-216">Azure Portalı'ndan Uygulama Hizmeti'ne gidin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="6bbe2-217">Web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-217">Select the web app.</span></span>
1. <span data-ttu-id="6bbe2-218">"Uzantılar" için filtre lemek için arama kutusuna "ex" yazın veya yönetim araçları listesini aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="6bbe2-219">**Uzantılar**'ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="6bbe2-220">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-220">Select **Add**.</span></span>
1. <span data-ttu-id="6bbe2-221">listeden **ASP.NET Core {X.Y} ({x64|x86}) Runtime** `{X.Y}` uzantısını seçin, ASP.NET `{x64|x86}` Core önizleme sürümü ve platformu belirtir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="6bbe2-222">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="6bbe2-223">Uzantıyı yüklemek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="6bbe2-224">İşlem tamamlandığında, en son .NET Core önizlemesi yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="6bbe2-225">Yüklemeyi doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-225">Verify the installation:</span></span>

1. <span data-ttu-id="6bbe2-226">**Gelişmiş Araçlar'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="6bbe2-227">**Gelişmiş Araçlarda** **Git'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="6bbe2-228">Hata **Ayıklama konsolu** > **PowerShell** menü öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="6bbe2-229">PowerShell komut isteminde aşağıdaki komutu uygulayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="6bbe2-230">ASP.NET Core çalışma zamanı `{X.Y}` sürümünü ve `{PLATFORM}` platformu komutta yerine:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="6bbe2-231">x64 `True` önizleme çalışma süresi yüklendiğinde komut geri döner.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="6bbe2-232">Bir Uygulama Hizmetleri uygulamasının platform mimarisi (x86/x64), A serisi bilgi işlem (Temel) veya daha yüksek barındırma katmanında barındırılan uygulamalar için Uygulamanın Azure Portalı'ndaki ayarlarında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="6bbe2-233">Uygulamanın yayımlama ayarlarının (örneğin, Visual Studio [yayımlama profilinde (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) Uygulamanın Azure portalındaki hizmet yapılandırmasındaki ayarla eşleşin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="6bbe2-234">Uygulama işlem modunda çalıştırılırsa ve platform mimarisi 64 bit (x64) için yapılandırılırsa, ASP.NET Çekirdek Modülü varsa 64 bit önizleme çalışma süresini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="6bbe2-235">Azure Portalını kullanarak **Core {X.Y} (x64) Çalışma Süresi uzantısıASP.NET** yükleyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="6bbe2-236">x64 önizleme çalışma süresini yükledikten sonra, yüklemeyi doğrulamak için Azure Kudu PowerShell komut penceresinde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="6bbe2-237">ASP.NET Core çalışma zamanı `{X.Y}` sürümünü aşağıdaki komutla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="6bbe2-238">x64 `True` önizleme çalışma süresi yüklendiğinde komut geri döner.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="6bbe2-239">**ASP.NET Core Uzantıları,** Azure Uygulama Hizmetleri'nde ASP.NET Core için Azure günlüğe kaydetmeyi etkinleştirme gibi ek işlevsellik sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="6bbe2-240">Uzantı Visual Studio'dan dağıtılırken otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="6bbe2-241">Uzantı yüklenmiyorsa, uygulama için yükleyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="6bbe2-242">**ÖNIZLEME sitesi uzantısını ARM şablonuyla kullanma**</span><span class="sxs-lookup"><span data-stu-id="6bbe2-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="6bbe2-243">Uygulamaları oluşturmak ve dağıtmak için ARM şablonu kullanılıyorsa, `siteextensions` kaynak türü site uzantısını bir web uygulamasına eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="6bbe2-244">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="6bbe2-245">Uygulamayı yayımlama ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6bbe2-246">64 bit dağıtım için:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="6bbe2-247">64 bit uygulama oluşturmak için 64 bit .NET Core SDK kullanın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="6bbe2-248">Uygulama Hizmetinin **Yapılandırma** > **Genel ayarlarında** **Platformu** **64 Bit** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="6bbe2-249">Uygulama, platform bitness seçimini etkinleştirmek için bir Temel veya daha yüksek hizmet planı kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="6bbe2-250">Uygulama çerçevesine bağlı dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6bbe2-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bbe2-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6bbe2-252">Visual Studio araç çubuğundan > **"Yayınla "Yayımla** "Tamamla"yı seçin veya **Solution Explorer'daki** projeyi sağ tıklatın ve **Yayımla'yı**seçin. **Build**</span><span class="sxs-lookup"><span data-stu-id="6bbe2-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="6bbe2-253">**Yayımlama hedef** iletişim kutusunda, **Uygulama Hizmeti'nin** seçildiğini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="6bbe2-254">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-254">Select **Advanced**.</span></span> <span data-ttu-id="6bbe2-255">**Yayımla** iletişim kutusu açılır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="6bbe2-256">**Yayımla** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="6bbe2-257">**Sürüm** yapılandırmasının seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="6bbe2-258">Dağıtım **Modu** açılır listesini açın ve **Framework'e Bağlı'yı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="6bbe2-259">Hedef Çalışma **Zamanı**olarak **Taşınabilir'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="6bbe2-260">Dağıtım sırasında ek dosyaları kaldırmanız gerekiyorsa, **Dosya Yayımlama Seçenekleri'ni** açın ve hedefteki ek dosyaları kaldırmak için onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="6bbe2-261">**Kaydet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-261">Select **Save**.</span></span>
1. <span data-ttu-id="6bbe2-262">Yayımlama sihirbazının kalan istemlerini izleyerek yeni bir site oluşturun veya varolan bir siteyi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6bbe2-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6bbe2-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6bbe2-264">Proje dosyasında, [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog)belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="6bbe2-265">Komut kabuğundan, [uygulamayı dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuyla Serbest Bırak yapılandırmasında yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="6bbe2-266">Aşağıdaki örnekte, uygulama çerçeveye bağımlı bir uygulama olarak yayınlanır:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="6bbe2-267">*Depo gözü/Yayın/{TARGET FRAMEWORK}/yayın* dizininin içeriğini App Service'deki siteye taşıyın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="6bbe2-268">Yerel sabit diskinizden veya ağ paylaşımınızdan *yayımlama* klasörü içeriğini doğrudan Kudu konsolundaki App Service'e sürüklüyorsanız, dosyaları [Kudu](https://github.com/projectkudu/kudu/wiki) konsolundaki `D:\home\site\wwwroot` klasöre sürükleyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="6bbe2-269">Uygulamayı kendi kendine barındıran dağıtma</span><span class="sxs-lookup"><span data-stu-id="6bbe2-269">Deploy the app self-contained</span></span>

<span data-ttu-id="6bbe2-270">[Kendi kendine yeten bir dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)için Visual Studio veya .NET Core CLI'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-270">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6bbe2-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bbe2-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6bbe2-272">Visual Studio araç çubuğundan > **"Yayınla "Yayımla** "Tamamla"yı seçin veya **Solution Explorer'daki** projeyi sağ tıklatın ve **Yayımla'yı**seçin. **Build**</span><span class="sxs-lookup"><span data-stu-id="6bbe2-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="6bbe2-273">**Yayımlama hedef** iletişim kutusunda, **Uygulama Hizmeti'nin** seçildiğini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="6bbe2-274">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-274">Select **Advanced**.</span></span> <span data-ttu-id="6bbe2-275">**Yayımla** iletişim kutusu açılır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="6bbe2-276">**Yayımla** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="6bbe2-277">**Sürüm** yapılandırmasının seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="6bbe2-278">Dağıtım **Modu** açılır listesini açın ve **Bağımsız İçeriği'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="6bbe2-279">**Hedef Çalışma Zamanı** açılır listesinden hedef çalışma süresini seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="6bbe2-280">Varsayılan değer: `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="6bbe2-281">Dağıtım sırasında ek dosyaları kaldırmanız gerekiyorsa, **Dosya Yayımlama Seçenekleri'ni** açın ve hedefteki ek dosyaları kaldırmak için onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="6bbe2-282">**Kaydet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-282">Select **Save**.</span></span>
1. <span data-ttu-id="6bbe2-283">Yayımlama sihirbazının kalan istemlerini izleyerek yeni bir site oluşturun veya varolan bir siteyi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6bbe2-284">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6bbe2-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6bbe2-285">Proje dosyasında, bir veya daha fazla [Runtime Tanımlayıcısı (RIDs) belirtin.](/dotnet/core/rid-catalog)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="6bbe2-286">Tek `<RuntimeIdentifier>` bir RID için (tekil) kullanın veya yarı sütunlu sınırlı bir RID listesi sağlamak için (çoğul) kullanın. `<RuntimeIdentifiers>`</span><span class="sxs-lookup"><span data-stu-id="6bbe2-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="6bbe2-287">Aşağıdaki örnekte, `win-x86` RID belirtilir:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="6bbe2-288">Komut kabuğundan, [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuyla ana bilgisayar çalışma zamanı için uygulamayı Sürüm yapılandırmasında yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="6bbe2-289">Aşağıdaki örnekte, uygulama `win-x86` RID için yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="6bbe2-290">`--runtime` Opsiyona sağlanan RID, proje dosyasındaki `<RuntimeIdentifier>` (veya) `<RuntimeIdentifiers>`özellikte sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="6bbe2-291">*Depo gözü/Yayın/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* dizininin içeriğini App Service'deki siteye taşıyın.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="6bbe2-292">Yerel sabit diskinizden veya ağ paylaşımınızdan *yayımlama* klasörü içeriğini doğrudan Kudu konsolundaki App Service'e sürüklüyorsanız, dosyaları Kudu konsolundaki `D:\home\site\wwwroot` klasöre sürükleyin.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="6bbe2-293">Protokol ayarları (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="6bbe2-294">Güvenli iletişim kuralı bağlamaları, HTTPS üzerinden gelen isteklere yanıt verirken kullanılacak bir sertifika belirtmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="6bbe2-295">Bağlama, belirli bir ana bilgisayar adı için verilen geçerli bir özel sertifika *(.pfx)* gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="6bbe2-296">Daha fazla bilgi için [Bkz. Öğretici: Varolan özel bir SSL sertifikasını Azure Uygulama Hizmetine bağla.](/azure/app-service/app-service-web-tutorial-custom-ssl)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="6bbe2-297">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="6bbe2-297">Transform web.config</span></span>

<span data-ttu-id="6bbe2-298">Yayımlamada *web.config'i* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama <xref:host-and-deploy/iis/transform-webconfig>göre ortam değişkenleri ayarlayın), bkz.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6bbe2-299">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6bbe2-299">Additional resources</span></span>

* [<span data-ttu-id="6bbe2-300">App Service’e genel bakış</span><span class="sxs-lookup"><span data-stu-id="6bbe2-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="6bbe2-301">Azure Uygulama Hizmeti: .NET Uygulamalarınızda Ev Sahipliği Yapmak Için En İyi Yer (55 dakikalık genel bakış videosu)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="6bbe2-302">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="6bbe2-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="6bbe2-303">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="6bbe2-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="6bbe2-304">Windows Server'daki Azure Uygulama [Hizmeti, Internet Bilgi Hizmetleri (IIS)](https://www.iis.net/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="6bbe2-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="6bbe2-305">Aşağıdaki konular temel IIS teknolojisi ile ilgilidir:</span><span class="sxs-lookup"><span data-stu-id="6bbe2-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="6bbe2-306">Windows Server - Geçerli ve önceki sürümler için BT yöneticisi içeriği</span><span class="sxs-lookup"><span data-stu-id="6bbe2-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
