---
title: ASP.NET Core barındırma ve dağıtma
author: rick-anderson
description: Barındırma ortamlarını ayarlamayı ve ASP.NET Core uygulamaları dağıtmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556625"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="94034-103">ASP.NET Core barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="94034-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="94034-104">Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="94034-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="94034-105">Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="94034-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="94034-106">İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="94034-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="94034-107">Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="94034-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="94034-108">Klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="94034-108">Publish to a folder</span></span>

<span data-ttu-id="94034-109">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="94034-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="94034-110">Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="94034-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="94034-111">Yayımlama ayarları dosyaları</span><span class="sxs-lookup"><span data-stu-id="94034-111">Publish settings files</span></span>

<span data-ttu-id="94034-112">`*.json` dosyalar varsayılan olarak yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="94034-112">`*.json` files are published by default.</span></span> <span data-ttu-id="94034-113">Diğer ayar dosyalarını yayımlamak için bunları [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) Proje dosyasındaki bir öğe içinde belirtin.</span><span class="sxs-lookup"><span data-stu-id="94034-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="94034-114">Aşağıdaki örnek XML dosyalarını yayımlar:</span><span class="sxs-lookup"><span data-stu-id="94034-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="94034-115">Klasör içeriği</span><span class="sxs-lookup"><span data-stu-id="94034-115">Folder contents</span></span>

<span data-ttu-id="94034-116">*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.</span><span class="sxs-lookup"><span data-stu-id="94034-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="94034-117">.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım* olarak yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="94034-118">Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="94034-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="94034-119">Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="94034-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="94034-120">Varsayılan dağıtım modeli çerçeveye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="94034-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="94034-121">Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="94034-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="94034-122">*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="94034-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="94034-123">Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="94034-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="94034-124">İşlem Yöneticisi ayarlama</span><span class="sxs-lookup"><span data-stu-id="94034-124">Set up a process manager</span></span>

<span data-ttu-id="94034-125">ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="94034-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="94034-126">Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="94034-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="94034-127">ASP.NET Core için en yaygın işlem yöneticileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="94034-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="94034-128">Linux</span><span class="sxs-lookup"><span data-stu-id="94034-128">Linux</span></span>
  * [<span data-ttu-id="94034-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="94034-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="94034-130">Apache</span><span class="sxs-lookup"><span data-stu-id="94034-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="94034-131">Windows</span><span class="sxs-lookup"><span data-stu-id="94034-131">Windows</span></span>
  * [<span data-ttu-id="94034-132">IIS</span><span class="sxs-lookup"><span data-stu-id="94034-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="94034-133">Windows hizmeti</span><span class="sxs-lookup"><span data-stu-id="94034-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="94034-134">Ters proxy ayarlama</span><span class="sxs-lookup"><span data-stu-id="94034-134">Set up a reverse proxy</span></span>

<span data-ttu-id="94034-135">Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="94034-136">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="94034-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="94034-137">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="94034-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="94034-138">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="94034-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="94034-139">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="94034-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="94034-140">Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="94034-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="94034-141">Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="94034-142">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="94034-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="94034-143">Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma</span><span class="sxs-lookup"><span data-stu-id="94034-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="94034-144">Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="94034-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="94034-145">Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz.</span><span class="sxs-lookup"><span data-stu-id="94034-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="94034-146">Visual Studio Web dağıtımı için [MSBuild](/visualstudio/msbuild/msbuild) 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="94034-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="94034-147">Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="94034-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="94034-148">Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="94034-149">Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler.</span><span class="sxs-lookup"><span data-stu-id="94034-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="94034-150">Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="94034-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="94034-151">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="94034-151">Publish to Azure</span></span>

<span data-ttu-id="94034-152"><xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="94034-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="94034-153">[Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="94034-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="94034-154">Windows 'ta MSDeploy ile Yayımla</span><span class="sxs-lookup"><span data-stu-id="94034-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="94034-155">Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="94034-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="94034-156">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="94034-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="94034-157">*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="94034-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="94034-158">Web grubunda barındırma</span><span class="sxs-lookup"><span data-stu-id="94034-158">Host in a web farm</span></span>

<span data-ttu-id="94034-159">Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..</span><span class="sxs-lookup"><span data-stu-id="94034-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="94034-160">Docker 'da barındırma</span><span class="sxs-lookup"><span data-stu-id="94034-160">Host on Docker</span></span>

<span data-ttu-id="94034-161">Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="94034-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="94034-162">Sistem durumu denetimleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="94034-162">Perform health checks</span></span>

<span data-ttu-id="94034-163">Bir uygulamada ve bağımlılıklarında sistem durumu denetimleri gerçekleştirmek için sistem durumu denetimi ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="94034-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="94034-164">Daha fazla bilgi için bkz. <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="94034-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94034-165">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94034-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="94034-166">ASP.NET barındırma</span><span class="sxs-lookup"><span data-stu-id="94034-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="94034-167">Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="94034-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="94034-168">Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="94034-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="94034-169">İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="94034-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="94034-170">Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="94034-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="94034-171">Klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="94034-171">Publish to a folder</span></span>

<span data-ttu-id="94034-172">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="94034-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="94034-173">Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="94034-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="94034-174">Klasör içeriği</span><span class="sxs-lookup"><span data-stu-id="94034-174">Folder contents</span></span>

<span data-ttu-id="94034-175">*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.</span><span class="sxs-lookup"><span data-stu-id="94034-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="94034-176">.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım* olarak yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="94034-177">Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="94034-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="94034-178">Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="94034-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="94034-179">Varsayılan dağıtım modeli çerçeveye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="94034-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="94034-180">Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="94034-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="94034-181">*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="94034-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="94034-182">Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="94034-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="94034-183">İşlem Yöneticisi ayarlama</span><span class="sxs-lookup"><span data-stu-id="94034-183">Set up a process manager</span></span>

<span data-ttu-id="94034-184">ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="94034-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="94034-185">Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="94034-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="94034-186">ASP.NET Core için en yaygın işlem yöneticileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="94034-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="94034-187">Linux</span><span class="sxs-lookup"><span data-stu-id="94034-187">Linux</span></span>
  * [<span data-ttu-id="94034-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="94034-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="94034-189">Apache</span><span class="sxs-lookup"><span data-stu-id="94034-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="94034-190">Windows</span><span class="sxs-lookup"><span data-stu-id="94034-190">Windows</span></span>
  * [<span data-ttu-id="94034-191">IIS</span><span class="sxs-lookup"><span data-stu-id="94034-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="94034-192">Windows hizmeti</span><span class="sxs-lookup"><span data-stu-id="94034-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="94034-193">Ters proxy ayarlama</span><span class="sxs-lookup"><span data-stu-id="94034-193">Set up a reverse proxy</span></span>

<span data-ttu-id="94034-194">Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="94034-195">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="94034-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="94034-196">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="94034-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="94034-197">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="94034-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="94034-198">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="94034-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="94034-199">Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="94034-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="94034-200">Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="94034-201">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="94034-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="94034-202">Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma</span><span class="sxs-lookup"><span data-stu-id="94034-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="94034-203">Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="94034-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="94034-204">Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz.</span><span class="sxs-lookup"><span data-stu-id="94034-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="94034-205">Visual Studio Web dağıtımı için MSBuild 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="94034-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="94034-206">Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="94034-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="94034-207">Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="94034-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="94034-208">Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler.</span><span class="sxs-lookup"><span data-stu-id="94034-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="94034-209">Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="94034-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="94034-210">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="94034-210">Publish to Azure</span></span>

<span data-ttu-id="94034-211"><xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="94034-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="94034-212">[Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="94034-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="94034-213">Windows 'ta MSDeploy ile Yayımla</span><span class="sxs-lookup"><span data-stu-id="94034-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="94034-214">Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="94034-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="94034-215">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="94034-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="94034-216">*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="94034-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="94034-217">Web grubunda barındırma</span><span class="sxs-lookup"><span data-stu-id="94034-217">Host in a web farm</span></span>

<span data-ttu-id="94034-218">Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..</span><span class="sxs-lookup"><span data-stu-id="94034-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="94034-219">Docker 'da barındırma</span><span class="sxs-lookup"><span data-stu-id="94034-219">Host on Docker</span></span>

<span data-ttu-id="94034-220">Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="94034-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94034-221">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94034-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="94034-222">ASP.NET barındırma</span><span class="sxs-lookup"><span data-stu-id="94034-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
