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
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252974"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="07dc2-103">ASP.NET Core barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="07dc2-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="07dc2-104">Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="07dc2-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="07dc2-105">Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="07dc2-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="07dc2-106">İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="07dc2-107">Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="07dc2-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="07dc2-108">Klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-108">Publish to a folder</span></span>

<span data-ttu-id="07dc2-109">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="07dc2-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="07dc2-110">Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="07dc2-111">Yayımlama ayarları dosyaları</span><span class="sxs-lookup"><span data-stu-id="07dc2-111">Publish settings files</span></span>

<span data-ttu-id="07dc2-112">`*.json` dosyalar varsayılan olarak yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-112">`*.json` files are published by default.</span></span> <span data-ttu-id="07dc2-113">Diğer ayar dosyalarını yayımlamak için bunları [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) Proje dosyasındaki bir öğe içinde belirtin.</span><span class="sxs-lookup"><span data-stu-id="07dc2-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="07dc2-114">Aşağıdaki örnek XML dosyalarını yayımlar:</span><span class="sxs-lookup"><span data-stu-id="07dc2-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="07dc2-115">Klasör içeriği</span><span class="sxs-lookup"><span data-stu-id="07dc2-115">Folder contents</span></span>

<span data-ttu-id="07dc2-116">*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="07dc2-117">.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım* olarak yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="07dc2-118">Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="07dc2-119">Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="07dc2-120">Varsayılan dağıtım modeli çerçeveye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="07dc2-121">Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="07dc2-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="07dc2-122">*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="07dc2-123">Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="07dc2-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="07dc2-124">İşlem Yöneticisi ayarlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-124">Set up a process manager</span></span>

<span data-ttu-id="07dc2-125">ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="07dc2-126">Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="07dc2-127">ASP.NET Core için en yaygın işlem yöneticileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="07dc2-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="07dc2-128">Linux</span><span class="sxs-lookup"><span data-stu-id="07dc2-128">Linux</span></span>
  * [<span data-ttu-id="07dc2-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="07dc2-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="07dc2-130">Apache</span><span class="sxs-lookup"><span data-stu-id="07dc2-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="07dc2-131">Windows</span><span class="sxs-lookup"><span data-stu-id="07dc2-131">Windows</span></span>
  * [<span data-ttu-id="07dc2-132">IIS</span><span class="sxs-lookup"><span data-stu-id="07dc2-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="07dc2-133">Windows hizmeti</span><span class="sxs-lookup"><span data-stu-id="07dc2-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="07dc2-134">Ters proxy ayarlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-134">Set up a reverse proxy</span></span>

<span data-ttu-id="07dc2-135">Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="07dc2-136">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="07dc2-137">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="07dc2-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="07dc2-138">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="07dc2-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="07dc2-139">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="07dc2-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="07dc2-140">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="07dc2-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="07dc2-141">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="07dc2-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="07dc2-142">Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="07dc2-143">Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="07dc2-144">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="07dc2-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="07dc2-145">Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma</span><span class="sxs-lookup"><span data-stu-id="07dc2-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="07dc2-146">Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="07dc2-147">Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz.</span><span class="sxs-lookup"><span data-stu-id="07dc2-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="07dc2-148">Visual Studio Web dağıtımı için [MSBuild](/visualstudio/msbuild/msbuild) 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="07dc2-149">Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="07dc2-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="07dc2-150">Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="07dc2-151">Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler.</span><span class="sxs-lookup"><span data-stu-id="07dc2-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="07dc2-152">Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="07dc2-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="07dc2-153">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-153">Publish to Azure</span></span>

<span data-ttu-id="07dc2-154"><xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="07dc2-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="07dc2-155">[Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="07dc2-156">Windows 'ta MSDeploy ile Yayımla</span><span class="sxs-lookup"><span data-stu-id="07dc2-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="07dc2-157">Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="07dc2-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="07dc2-158">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="07dc2-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="07dc2-159">*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="07dc2-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="07dc2-160">Web grubunda barındırma</span><span class="sxs-lookup"><span data-stu-id="07dc2-160">Host in a web farm</span></span>

<span data-ttu-id="07dc2-161">Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..</span><span class="sxs-lookup"><span data-stu-id="07dc2-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="07dc2-162">Docker 'da barındırma</span><span class="sxs-lookup"><span data-stu-id="07dc2-162">Host on Docker</span></span>

<span data-ttu-id="07dc2-163">Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="07dc2-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="07dc2-164">Sistem durumu denetimleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="07dc2-164">Perform health checks</span></span>

<span data-ttu-id="07dc2-165">Bir uygulamada ve bağımlılıklarında sistem durumu denetimleri gerçekleştirmek için sistem durumu denetimi ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="07dc2-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="07dc2-166">Daha fazla bilgi için bkz. <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="07dc2-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07dc2-167">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="07dc2-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="07dc2-168">ASP.NET barındırma</span><span class="sxs-lookup"><span data-stu-id="07dc2-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="07dc2-169">Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="07dc2-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="07dc2-170">Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="07dc2-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="07dc2-171">İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="07dc2-172">Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="07dc2-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="07dc2-173">Klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-173">Publish to a folder</span></span>

<span data-ttu-id="07dc2-174">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="07dc2-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="07dc2-175">Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="07dc2-176">Klasör içeriği</span><span class="sxs-lookup"><span data-stu-id="07dc2-176">Folder contents</span></span>

<span data-ttu-id="07dc2-177">*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="07dc2-178">.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım* olarak yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="07dc2-179">Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="07dc2-180">Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="07dc2-181">Varsayılan dağıtım modeli çerçeveye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="07dc2-182">Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="07dc2-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="07dc2-183">*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="07dc2-184">Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="07dc2-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="07dc2-185">İşlem Yöneticisi ayarlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-185">Set up a process manager</span></span>

<span data-ttu-id="07dc2-186">ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="07dc2-187">Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="07dc2-188">ASP.NET Core için en yaygın işlem yöneticileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="07dc2-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="07dc2-189">Linux</span><span class="sxs-lookup"><span data-stu-id="07dc2-189">Linux</span></span>
  * [<span data-ttu-id="07dc2-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="07dc2-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="07dc2-191">Apache</span><span class="sxs-lookup"><span data-stu-id="07dc2-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="07dc2-192">Windows</span><span class="sxs-lookup"><span data-stu-id="07dc2-192">Windows</span></span>
  * [<span data-ttu-id="07dc2-193">IIS</span><span class="sxs-lookup"><span data-stu-id="07dc2-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="07dc2-194">Windows hizmeti</span><span class="sxs-lookup"><span data-stu-id="07dc2-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="07dc2-195">Ters proxy ayarlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-195">Set up a reverse proxy</span></span>

<span data-ttu-id="07dc2-196">Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="07dc2-197">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="07dc2-198">&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="07dc2-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="07dc2-199">Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="07dc2-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="07dc2-200">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="07dc2-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="07dc2-201">Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="07dc2-202">Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="07dc2-203">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="07dc2-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="07dc2-204">Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma</span><span class="sxs-lookup"><span data-stu-id="07dc2-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="07dc2-205">Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="07dc2-206">Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz.</span><span class="sxs-lookup"><span data-stu-id="07dc2-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="07dc2-207">Visual Studio Web dağıtımı için MSBuild 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="07dc2-208">Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="07dc2-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="07dc2-209">Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="07dc2-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="07dc2-210">Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler.</span><span class="sxs-lookup"><span data-stu-id="07dc2-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="07dc2-211">Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="07dc2-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="07dc2-212">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="07dc2-212">Publish to Azure</span></span>

<span data-ttu-id="07dc2-213"><xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="07dc2-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="07dc2-214">[Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="07dc2-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="07dc2-215">Windows 'ta MSDeploy ile Yayımla</span><span class="sxs-lookup"><span data-stu-id="07dc2-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="07dc2-216">Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..</span><span class="sxs-lookup"><span data-stu-id="07dc2-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="07dc2-217">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="07dc2-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="07dc2-218">*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="07dc2-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="07dc2-219">Web grubunda barındırma</span><span class="sxs-lookup"><span data-stu-id="07dc2-219">Host in a web farm</span></span>

<span data-ttu-id="07dc2-220">Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..</span><span class="sxs-lookup"><span data-stu-id="07dc2-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="07dc2-221">Docker 'da barındırma</span><span class="sxs-lookup"><span data-stu-id="07dc2-221">Host on Docker</span></span>

<span data-ttu-id="07dc2-222">Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="07dc2-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07dc2-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="07dc2-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="07dc2-224">ASP.NET barındırma</span><span class="sxs-lookup"><span data-stu-id="07dc2-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
