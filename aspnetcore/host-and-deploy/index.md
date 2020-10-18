---
title: ASP.NET Core barındırma ve dağıtma
author: rick-anderson
description: Barındırma ortamlarını ayarlamayı ve ASP.NET Core uygulamaları dağıtmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: host-and-deploy/index
ms.openlocfilehash: f8271d71f34487062484581ba6b3b8445a62439c
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113797"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="cbff8-103">ASP.NET Core barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="cbff8-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="cbff8-104">Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="cbff8-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="cbff8-105">Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="cbff8-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="cbff8-106">İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="cbff8-107">Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="cbff8-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="cbff8-108">Klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-108">Publish to a folder</span></span>

<span data-ttu-id="cbff8-109">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="cbff8-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="cbff8-110">Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="cbff8-111">Klasör içeriği</span><span class="sxs-lookup"><span data-stu-id="cbff8-111">Folder contents</span></span>

<span data-ttu-id="cbff8-112">*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="cbff8-113">.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım*olarak yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="cbff8-114">Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="cbff8-115">Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="cbff8-116">Varsayılan dağıtım modeli çerçeveye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="cbff8-117">Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="cbff8-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="cbff8-118">*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="cbff8-119">Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="cbff8-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="cbff8-120">İşlem Yöneticisi ayarlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-120">Set up a process manager</span></span>

<span data-ttu-id="cbff8-121">ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="cbff8-122">Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="cbff8-123">ASP.NET Core için en yaygın işlem yöneticileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="cbff8-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="cbff8-124">Linux</span><span class="sxs-lookup"><span data-stu-id="cbff8-124">Linux</span></span>
  * [<span data-ttu-id="cbff8-125">NGINX</span><span class="sxs-lookup"><span data-stu-id="cbff8-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="cbff8-126">Apache</span><span class="sxs-lookup"><span data-stu-id="cbff8-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="cbff8-127">Windows</span><span class="sxs-lookup"><span data-stu-id="cbff8-127">Windows</span></span>
  * [<span data-ttu-id="cbff8-128">IIS</span><span class="sxs-lookup"><span data-stu-id="cbff8-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="cbff8-129">Windows hizmeti</span><span class="sxs-lookup"><span data-stu-id="cbff8-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="cbff8-130">Ters proxy ayarlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-130">Set up a reverse proxy</span></span>

<span data-ttu-id="cbff8-131">Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="cbff8-132">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="cbff8-133">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="cbff8-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="cbff8-134">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="cbff8-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="cbff8-135">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="cbff8-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="cbff8-136">Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="cbff8-137">Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="cbff8-138">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="cbff8-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="cbff8-139">Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma</span><span class="sxs-lookup"><span data-stu-id="cbff8-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="cbff8-140">Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="cbff8-141">Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz.</span><span class="sxs-lookup"><span data-stu-id="cbff8-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="cbff8-142">Visual Studio Web dağıtımı için [MSBuild](/visualstudio/msbuild/msbuild) 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-142">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="cbff8-143">Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="cbff8-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="cbff8-144">Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="cbff8-145">Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler.</span><span class="sxs-lookup"><span data-stu-id="cbff8-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="cbff8-146">Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="cbff8-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="cbff8-147">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-147">Publish to Azure</span></span>

<span data-ttu-id="cbff8-148"><xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="cbff8-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="cbff8-149">[Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="cbff8-150">Windows 'ta MSDeploy ile Yayımla</span><span class="sxs-lookup"><span data-stu-id="cbff8-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="cbff8-151">Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="cbff8-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="cbff8-152">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="cbff8-152">Internet Information Services (IIS)</span></span>

<span data-ttu-id="cbff8-153">*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="cbff8-153">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="cbff8-154">Web grubunda barındırma</span><span class="sxs-lookup"><span data-stu-id="cbff8-154">Host in a web farm</span></span>

<span data-ttu-id="cbff8-155">Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..</span><span class="sxs-lookup"><span data-stu-id="cbff8-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="cbff8-156">Docker 'da barındırma</span><span class="sxs-lookup"><span data-stu-id="cbff8-156">Host on Docker</span></span>

<span data-ttu-id="cbff8-157">Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="cbff8-157">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="cbff8-158">Sistem durumu denetimleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="cbff8-158">Perform health checks</span></span>

<span data-ttu-id="cbff8-159">Bir uygulamada ve bağımlılıklarında sistem durumu denetimleri gerçekleştirmek için sistem durumu denetimi ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="cbff8-159">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="cbff8-160">Daha fazla bilgi için bkz. <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="cbff8-160">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cbff8-161">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cbff8-161">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="cbff8-162">ASP.NET barındırma</span><span class="sxs-lookup"><span data-stu-id="cbff8-162">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="cbff8-163">Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="cbff8-163">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="cbff8-164">Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="cbff8-164">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="cbff8-165">İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-165">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="cbff8-166">Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="cbff8-166">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="cbff8-167">Klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-167">Publish to a folder</span></span>

<span data-ttu-id="cbff8-168">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="cbff8-168">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="cbff8-169">Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-169">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="cbff8-170">Klasör içeriği</span><span class="sxs-lookup"><span data-stu-id="cbff8-170">Folder contents</span></span>

<span data-ttu-id="cbff8-171">*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-171">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="cbff8-172">.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım*olarak yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-172">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="cbff8-173">Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-173">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="cbff8-174">Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-174">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="cbff8-175">Varsayılan dağıtım modeli çerçeveye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-175">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="cbff8-176">Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="cbff8-176">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="cbff8-177">*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-177">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="cbff8-178">Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="cbff8-178">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="cbff8-179">İşlem Yöneticisi ayarlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-179">Set up a process manager</span></span>

<span data-ttu-id="cbff8-180">ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-180">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="cbff8-181">Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-181">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="cbff8-182">ASP.NET Core için en yaygın işlem yöneticileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="cbff8-182">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="cbff8-183">Linux</span><span class="sxs-lookup"><span data-stu-id="cbff8-183">Linux</span></span>
  * [<span data-ttu-id="cbff8-184">NGINX</span><span class="sxs-lookup"><span data-stu-id="cbff8-184">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="cbff8-185">Apache</span><span class="sxs-lookup"><span data-stu-id="cbff8-185">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="cbff8-186">Windows</span><span class="sxs-lookup"><span data-stu-id="cbff8-186">Windows</span></span>
  * [<span data-ttu-id="cbff8-187">IIS</span><span class="sxs-lookup"><span data-stu-id="cbff8-187">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="cbff8-188">Windows hizmeti</span><span class="sxs-lookup"><span data-stu-id="cbff8-188">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="cbff8-189">Ters proxy ayarlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-189">Set up a reverse proxy</span></span>

<span data-ttu-id="cbff8-190">Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-190">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="cbff8-191">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-191">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="cbff8-192">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="cbff8-192">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="cbff8-193">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="cbff8-193">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="cbff8-194">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="cbff8-194">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="cbff8-195">Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-195">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="cbff8-196">Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-196">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="cbff8-197">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="cbff8-197">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="cbff8-198">Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma</span><span class="sxs-lookup"><span data-stu-id="cbff8-198">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="cbff8-199">Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-199">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="cbff8-200">Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz.</span><span class="sxs-lookup"><span data-stu-id="cbff8-200">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="cbff8-201">Visual Studio Web dağıtımı için MSBuild 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-201">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="cbff8-202">Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="cbff8-202">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="cbff8-203">Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbff8-203">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="cbff8-204">Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler.</span><span class="sxs-lookup"><span data-stu-id="cbff8-204">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="cbff8-205">Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="cbff8-205">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="cbff8-206">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="cbff8-206">Publish to Azure</span></span>

<span data-ttu-id="cbff8-207"><xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="cbff8-207">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="cbff8-208">[Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cbff8-208">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="cbff8-209">Windows 'ta MSDeploy ile Yayımla</span><span class="sxs-lookup"><span data-stu-id="cbff8-209">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="cbff8-210">Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="cbff8-210">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="cbff8-211">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="cbff8-211">Internet Information Services (IIS)</span></span>

<span data-ttu-id="cbff8-212">*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="cbff8-212">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="cbff8-213">Web grubunda barındırma</span><span class="sxs-lookup"><span data-stu-id="cbff8-213">Host in a web farm</span></span>

<span data-ttu-id="cbff8-214">Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..</span><span class="sxs-lookup"><span data-stu-id="cbff8-214">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="cbff8-215">Docker 'da barındırma</span><span class="sxs-lookup"><span data-stu-id="cbff8-215">Host on Docker</span></span>

<span data-ttu-id="cbff8-216">Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="cbff8-216">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cbff8-217">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cbff8-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="cbff8-218">ASP.NET barındırma</span><span class="sxs-lookup"><span data-stu-id="cbff8-218">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
