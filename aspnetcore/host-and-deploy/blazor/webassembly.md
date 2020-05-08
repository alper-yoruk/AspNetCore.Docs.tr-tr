---
title: ASP.NET Core Blazor webassembly 'ı barındırma ve dağıtma
author: guardrex
description: ASP.NET Core, Içerik teslim ağları (CDN Blazor ), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: e136a401beffe9cc7e29906b3631ab3f068b30fd
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967603"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="6056a-103">Barındırma ve dağıtım ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6056a-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="6056a-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından</span><span class="sxs-lookup"><span data-stu-id="6056a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6056a-105">[Blazor WebAssembly barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="6056a-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="6056a-106">Blazor uygulaması, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="6056a-107">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="6056a-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="6056a-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="6056a-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="6056a-109">Blazor uygulaması, bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="6056a-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="6056a-110">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="6056a-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="6056a-111">Blazor uygulaması, .NET, Blazor uygulamasına hizmet vermek için kullanılmayan bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="6056a-112">Bu strateji, bir Blazor WebAssembly uygulamasını IIS alt uygulaması olarak barındırma hakkında bilgi içeren [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="6056a-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="6056a-113">Brotli ön sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="6056a-113">Brotli precompression</span></span>

<span data-ttu-id="6056a-114">Bir Blazor WebAssembly uygulaması yayımlandığında, çıkış en yüksek düzeydeki [Brotli sıkıştırma algoritması](https://tools.ietf.org/html/rfc7932) kullanılarak uygulama boyutunu azaltmak ve çalışma zamanı sıkıştırması gereksinimini kaldırmak için önceden sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="6056a-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="6056a-115">IIS *Web. config* sıkıştırma yapılandırması için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6056a-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="6056a-116">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="6056a-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="6056a-117">Bir Blazor WebAssembly uygulamasındaki sayfa bileşenlerine yönelik yönlendirme istekleri, bir Blazor sunucusu barındırılan uygulamasındaki yönlendirme istekleri kadar basittir.</span><span class="sxs-lookup"><span data-stu-id="6056a-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="6056a-118">İki bileşeni olan bir Blazor WebAssembly uygulaması düşünün:</span><span class="sxs-lookup"><span data-stu-id="6056a-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="6056a-119">*Main. Razor* &ndash; , uygulamanın köküne yüklenir ve `About` bileşene (`href="About"`) bir bağlantı içerir.</span><span class="sxs-lookup"><span data-stu-id="6056a-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="6056a-120">*. Razor* &ndash; `About` bileşeni hakkında.</span><span class="sxs-lookup"><span data-stu-id="6056a-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="6056a-121">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="6056a-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="6056a-122">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="6056a-122">The browser makes a request.</span></span>
1. <span data-ttu-id="6056a-123">Varsayılan sayfa döndürülür, bu genellikle *index. html*'dir.</span><span class="sxs-lookup"><span data-stu-id="6056a-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="6056a-124">uygulamanın *index. html* önyükleme.</span><span class="sxs-lookup"><span data-stu-id="6056a-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="6056a-125">Blazor 'in yönlendirici yükleri ve Razor `Main` bileşeni işlenir.</span><span class="sxs-lookup"><span data-stu-id="6056a-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="6056a-126">Ana sayfada, Blazor `About` yönlendiricisi tarayıcıyı Internet `www.contoso.com` 'te için bir istek yapmadan `About` ve işlenmiş `About` bileşenin kendisini görecek olduğundan, bileşen bağlantısını seçtiğinizde istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="6056a-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="6056a-127">*Blazor WebAssembly uygulamasındaki* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="6056a-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="6056a-128">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="6056a-128">The router handles the requests internally.</span></span>

<span data-ttu-id="6056a-129">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About`, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="6056a-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="6056a-130">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6056a-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="6056a-131">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri, fiziksel olarak sunucu üzerinde olmayan kaynakların tüm isteklerini *Dizin. html* sayfasına yeniden yazmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6056a-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="6056a-132">*İndex. html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi, doğru kaynakla yararlanır ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="6056a-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="6056a-133">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayınlanan *Web. config* dosyasıyla birlikte kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6056a-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="6056a-134">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6056a-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="6056a-135">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="6056a-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="6056a-136">*Barındırılan bir dağıtım* , Web sunucusu üzerinde çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcıları Blazor webassembly uygulamasına sunar.</span><span class="sxs-lookup"><span data-stu-id="6056a-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="6056a-137">Client Blazor WebAssembly uygulaması sunucu uygulamasının */BIN/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="6056a-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="6056a-138">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="6056a-138">The two apps are deployed together.</span></span> <span data-ttu-id="6056a-139">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6056a-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="6056a-140">Barındırılan bir dağıtım için Visual Studio, **barındırılan** `-ho|--hosted` seçenek seçili olan (" `dotnet new` komut kullanılırken) **Blazor webassembly uygulama** projesi şablonunu (`blazorwasm` [DotNet yeni](/dotnet/core/tools/dotnet-new) komut kullanılırken şablon) içerir.</span><span class="sxs-lookup"><span data-stu-id="6056a-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="6056a-141">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index>..</span><span class="sxs-lookup"><span data-stu-id="6056a-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6056a-142">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs>..</span><span class="sxs-lookup"><span data-stu-id="6056a-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="6056a-143">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="6056a-143">Standalone deployment</span></span>

<span data-ttu-id="6056a-144">*Tek başına dağıtım* , doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak Blazor WebAssembly uygulamasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="6056a-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="6056a-145">Herhangi bir statik dosya sunucusu Blazor uygulamasını sunabilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="6056a-146">Tek başına dağıtım varlıkları */BIN/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="6056a-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="6056a-147">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6056a-147">Azure App Service</span></span>

<span data-ttu-id="6056a-148">Blazor WebAssembly Apps, uygulamayı [IIS](#iis)'de barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-148">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="6056a-149">Linux için Azure App Service tek başına Blazor WebAssembly uygulaması dağıtmak Şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="6056a-149">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="6056a-150">Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="6056a-150">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="6056a-151">Bu senaryoyu etkinleştirmek için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="6056a-151">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="6056a-152">IIS</span><span class="sxs-lookup"><span data-stu-id="6056a-152">IIS</span></span>

<span data-ttu-id="6056a-153">IIS, Blazor uygulamaları için özellikli bir statik dosya sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="6056a-153">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="6056a-154">IIS 'yi Blazor barındıracak şekilde yapılandırmak için bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="6056a-154">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="6056a-155">Yayımlanan varlıklar */BIN/Release/{Target Framework}/Publish* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6056a-155">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="6056a-156">Web sunucusunda veya barındırma hizmetinde *Yayımlama* klasörünün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="6056a-156">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="6056a-157">Web. config</span><span class="sxs-lookup"><span data-stu-id="6056a-157">web.config</span></span>

<span data-ttu-id="6056a-158">Bir Blazor projesi yayımlandığında, aşağıdaki IIS yapılandırmasıyla bir *Web. config* dosyası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="6056a-158">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="6056a-159">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="6056a-159">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="6056a-160">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="6056a-160">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="6056a-161">*. JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="6056a-161">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="6056a-162">&ndash; *.*`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="6056a-162">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="6056a-163">*. WOFF* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6056a-163">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="6056a-164">*. woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6056a-164">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="6056a-165">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="6056a-165">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="6056a-166">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="6056a-166">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="6056a-167">Uygulamanın statik varlıklarının bulunduğu alt dizini (*Wwwroot/{Path istenen}*) sunar.</span><span class="sxs-lookup"><span data-stu-id="6056a-167">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="6056a-168">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe (*Wwwroot/index.html*) uygulamanın varsayılan belgesine yeniden YÖNLENDIRILMESI için Spa geri dönüş yönlendirmesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6056a-168">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="6056a-169">Özel Web. config kullanma</span><span class="sxs-lookup"><span data-stu-id="6056a-169">Use a custom web.config</span></span>

<span data-ttu-id="6056a-170">Özel *Web. config* dosyasını kullanmak için, özel *Web. config* dosyasını proje klasörünün köküne yerleştirin ve projeyi yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-170">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="6056a-171">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="6056a-171">Install the URL Rewrite Module</span></span>

<span data-ttu-id="6056a-172">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6056a-172">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="6056a-173">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="6056a-173">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="6056a-174">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6056a-174">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="6056a-175">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="6056a-175">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="6056a-176">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="6056a-176">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="6056a-177">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6056a-177">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="6056a-178">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="6056a-178">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="6056a-179">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-179">Copy the installer to the server.</span></span> <span data-ttu-id="6056a-180">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6056a-180">Run the installer.</span></span> <span data-ttu-id="6056a-181">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="6056a-181">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="6056a-182">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="6056a-182">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="6056a-183">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6056a-183">Configure the website</span></span>

<span data-ttu-id="6056a-184">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-184">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="6056a-185">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="6056a-185">The folder contains:</span></span>

* <span data-ttu-id="6056a-186">Gerekli yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS 'nin Web sitesini yapılandırmak için kullandığı *Web. config* dosyası.</span><span class="sxs-lookup"><span data-stu-id="6056a-186">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="6056a-187">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="6056a-187">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="6056a-188">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="6056a-188">Host as an IIS sub-app</span></span>

<span data-ttu-id="6056a-189">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="6056a-189">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="6056a-190">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="6056a-190">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="6056a-191">Dosyaya bir `<handlers>` bölüm ekleyerek Blazor uygulamasının yayınlanan *Web. config* dosyasındaki işleyiciyi kaldırın:</span><span class="sxs-lookup"><span data-stu-id="6056a-191">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="6056a-192">Şu `<system.webServer>` şekilde `<location>` `inheritInChildApplications` ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın: `false`</span><span class="sxs-lookup"><span data-stu-id="6056a-192">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="6056a-193">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:host-and-deploy/blazor/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-193">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="6056a-194">Uygulamanın *index. html* dosyasındaki uygulama temel yolunu, IIS 'de alt uygulamayı YAPıLANDıRıRKEN kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-194">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="6056a-195">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="6056a-195">Brotli and Gzip compression</span></span>

<span data-ttu-id="6056a-196">IIS, *Web. config* aracılığıyla Brotli veya gzip sıkıştırılmış Blazor varlıklarını sunacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-196">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="6056a-197">Örnek bir yapılandırma için bkz. [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="6056a-197">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="6056a-198">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="6056a-198">Troubleshooting</span></span>

<span data-ttu-id="6056a-199">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-199">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="6056a-200">Modül yüklü olmadığında, *Web. config* dosyası IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="6056a-200">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="6056a-201">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesinin Blazor 'in statik dosyalarına hizmet etmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="6056a-201">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="6056a-202">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için <xref:test/troubleshoot-azure-iis>bkz..</span><span class="sxs-lookup"><span data-stu-id="6056a-202">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="6056a-203">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="6056a-203">Azure Storage</span></span>

<span data-ttu-id="6056a-204">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulamasının barındırılmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6056a-204">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="6056a-205">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="6056a-205">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="6056a-206">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="6056a-206">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="6056a-207">**Dizin belgesi adını** olarak `index.html`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-207">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="6056a-208">**Hata belge yolunu** olarak `index.html`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-208">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="6056a-209">Razor bileşenleri ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="6056a-209">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="6056a-210">Blazor yönlendiricisinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında, blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna**yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="6056a-210">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="6056a-211">*İndex. html* blobu döndürülür ve Blazor yönlendiricisi yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="6056a-211">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="6056a-212">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="6056a-212">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="6056a-213">Nginx</span><span class="sxs-lookup"><span data-stu-id="6056a-213">Nginx</span></span>

<span data-ttu-id="6056a-214">Aşağıdaki *NGINX. conf* dosyası, NGINX 'in, disk üzerinde karşılık gelen bir dosyayı bulamadığı her seferinde *index. html* dosyasını göndermek üzere nasıl yapılandırılacağını gösterecek şekilde basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6056a-214">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="6056a-215">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="6056a-215">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="6056a-216">Docker 'da NGINX</span><span class="sxs-lookup"><span data-stu-id="6056a-216">Nginx in Docker</span></span>

<span data-ttu-id="6056a-217">NGINX kullanarak Docker 'da Blazor barındırmak için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-217">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="6056a-218">Dockerfile dosyasını, *NGINX. config* dosyasını kapsayıcıya kopyalamak için güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-218">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="6056a-219">Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6056a-219">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="6056a-220">Apache</span><span class="sxs-lookup"><span data-stu-id="6056a-220">Apache</span></span>

<span data-ttu-id="6056a-221">CentOS 7 veya sonraki bir Blazor WebAssembly uygulamasını dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="6056a-221">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="6056a-222">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6056a-222">Create the Apache configuration file.</span></span> <span data-ttu-id="6056a-223">Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="6056a-223">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="6056a-224">Apache yapılandırma dosyasını, CentOS 7 `/etc/httpd/conf.d/` ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-224">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="6056a-225">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin (yapılandırma dosyasında belirtilen `DocumentRoot` konum).</span><span class="sxs-lookup"><span data-stu-id="6056a-225">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="6056a-226">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="6056a-226">Restart the Apache service.</span></span>

<span data-ttu-id="6056a-227">Daha fazla bilgi için bkz. [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="6056a-227">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="6056a-228">GitHub sayfaları</span><span class="sxs-lookup"><span data-stu-id="6056a-228">GitHub Pages</span></span>

<span data-ttu-id="6056a-229">URL yeniden işlemesini işlemek için, isteği *index. html* sayfasına yönlendirmeyi işleyen bir betiği olan bir *404. html* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6056a-229">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="6056a-230">Topluluk tarafından sunulan örnek bir uygulama için bkz. [GitHub sayfaları Için tek sayfalı uygulamalar](https://spa-github-pages.rafrex.com/) ([GitHub üzerinde rafrex/Spa-GitHub-Pages](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="6056a-230">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="6056a-231">Topluluk yaklaşımını kullanan bir örnek, GitHub ([canlı site](https://blazor-demo.github.io/)) [üzerinde blazor-demo/blazor-demo. GitHub. IO](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-231">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="6056a-232">Bir kuruluş sitesi yerine bir proje sitesi kullanırken `<base>` etiketi *index. html*dosyasına ekleyin veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-232">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="6056a-233">`href` Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin, `my-repository/`) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6056a-233">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6056a-234">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="6056a-234">Host configuration values</span></span>

<span data-ttu-id="6056a-235">[Blazor WebAssembly Apps](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-235">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="6056a-236">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="6056a-236">Content root</span></span>

<span data-ttu-id="6056a-237">`--contentroot` Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6056a-237">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="6056a-238">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="6056a-238">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="6056a-239">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-239">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6056a-240">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6056a-240">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="6056a-241">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6056a-241">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6056a-242">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile `dotnet run`bir komut isteminden çalıştırıldığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6056a-242">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="6056a-243">Visual Studio 'da, **Özellikler** > **hata ayıklama** > **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="6056a-243">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6056a-244">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="6056a-244">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="6056a-245">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="6056a-245">Path base</span></span>

<span data-ttu-id="6056a-246">Bağımsız `--pathbase` değişkeni, kök olmayan GÖRELI bir URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket `href` , hazırlama ve üretim `/` için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="6056a-246">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="6056a-247">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="6056a-247">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="6056a-248">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="6056a-248">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6056a-249">Etiketinde belirtilen `href` yolun aksine,`/` `--pathbase` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin. `<base>`</span><span class="sxs-lookup"><span data-stu-id="6056a-249">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="6056a-250">`<base>` Etikette uygulama temel yolu (sondaki eğik çizgi içeriyorsa) olarak `<base href="/CoolApp/">` sağlanmışsa, komut satırı bağımsız değişken değerini (sondaki eğik çizgi yok) olarak `--pathbase=/CoolApp` geçirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-250">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="6056a-251">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-251">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6056a-252">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6056a-252">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="6056a-253">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6056a-253">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6056a-254">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile `dotnet run`bir komut isteminden çalıştırırken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6056a-254">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="6056a-255">Visual Studio 'da, **Özellikler** > **hata ayıklama** > **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="6056a-255">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6056a-256">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="6056a-256">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="6056a-257">URL’ler</span><span class="sxs-lookup"><span data-stu-id="6056a-257">URLs</span></span>

<span data-ttu-id="6056a-258">`--urls` Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve PROTOKOLLERLE IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6056a-258">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="6056a-259">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-259">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6056a-260">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6056a-260">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="6056a-261">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6056a-261">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6056a-262">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile `dotnet run`bir komut isteminden çalıştırırken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6056a-262">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="6056a-263">Visual Studio 'da, **Özellikler** > **hata ayıklama** > **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="6056a-263">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6056a-264">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="6056a-264">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="6056a-265">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6056a-265">Configure the Linker</span></span>

<span data-ttu-id="6056a-266">Blazor, çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="6056a-266">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="6056a-267">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="6056a-267">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="6056a-268">Özel önyükleme kaynağı yükleme</span><span class="sxs-lookup"><span data-stu-id="6056a-268">Custom boot resource loading</span></span>

<span data-ttu-id="6056a-269">Yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için `loadBootResource` işleviyle birlikte bir Blazor WebAssembly uygulaması başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="6056a-269">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="6056a-270">Aşağıdaki `loadBootResource` senaryolar için kullanın:</span><span class="sxs-lookup"><span data-stu-id="6056a-270">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="6056a-271">Kullanıcıların bir CDN 'den saat dilimi verileri veya *DotNet. IStream* gibi statik kaynakları yüklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="6056a-271">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="6056a-272">Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="6056a-272">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="6056a-273">Her `fetch` isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları.</span><span class="sxs-lookup"><span data-stu-id="6056a-273">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="6056a-274">`loadBootResource`Parametreler aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6056a-274">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="6056a-275">Parametre</span><span class="sxs-lookup"><span data-stu-id="6056a-275">Parameter</span></span>    | <span data-ttu-id="6056a-276">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6056a-276">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="6056a-277">Kaynağın türü.</span><span class="sxs-lookup"><span data-stu-id="6056a-277">The type of the resource.</span></span> <span data-ttu-id="6056a-278">İzinleri olan türler: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="6056a-278">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="6056a-279">Kaynağın adı.</span><span class="sxs-lookup"><span data-stu-id="6056a-279">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="6056a-280">Kaynağın göreli veya mutlak URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="6056a-280">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="6056a-281">Yanıtta beklenen içeriği temsil eden bütünlük dizesi.</span><span class="sxs-lookup"><span data-stu-id="6056a-281">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="6056a-282">`loadBootResource`yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:</span><span class="sxs-lookup"><span data-stu-id="6056a-282">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="6056a-283">URI dizesi.</span><span class="sxs-lookup"><span data-stu-id="6056a-283">URI string.</span></span> <span data-ttu-id="6056a-284">Aşağıdaki örnekte (*Wwwroot/index.html*), aşağıdaki dosyalar ŞURADA `https://my-awesome-cdn.com/`bir CDN 'den sunulur:</span><span class="sxs-lookup"><span data-stu-id="6056a-284">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="6056a-285">*olmalı. \*. js*</span><span class="sxs-lookup"><span data-stu-id="6056a-285">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="6056a-286">*DotNet.*</span><span class="sxs-lookup"><span data-stu-id="6056a-286">*dotnet.wasm*</span></span>
  * <span data-ttu-id="6056a-287">Saat dilimi verileri</span><span class="sxs-lookup"><span data-stu-id="6056a-287">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="6056a-288">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="6056a-288">`Promise<Response>`.</span></span> <span data-ttu-id="6056a-289">Varsayılan bütünlük `integrity` denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.</span><span class="sxs-lookup"><span data-stu-id="6056a-289">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="6056a-290">Aşağıdaki örnek (*Wwwroot/index.html*) giden isteklere özel bir http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:</span><span class="sxs-lookup"><span data-stu-id="6056a-290">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="6056a-291">`null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.</span><span class="sxs-lookup"><span data-stu-id="6056a-291">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="6056a-292">Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="6056a-292">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="6056a-293">CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6056a-293">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="6056a-294">Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="6056a-294">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="6056a-295">İçin `loadBootResource` belirtilmemiş türler, varsayılan yükleme davranışları başına Framework tarafından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6056a-295">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="6056a-296">DLL dosyalarının dosya adı uzantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="6056a-296">Change the filename extension of DLL files</span></span>

<span data-ttu-id="6056a-297">Uygulamanın yayımlanmış *. dll* dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa, bu bölümdeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="6056a-297">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="6056a-298">Uygulamayı yayımladıktan sonra, *. dll* dosyalarını farklı bir dosya uzantısı kullanacak şekilde yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6056a-298">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="6056a-299">Uygulamanın yayımlanmış çıktısının *Wwwroot* dizinindeki *. dll* dosyalarını hedefleyin (ÖRNEĞIN, *{Content root}/bin/Release/netstandard2.1/Publish/Wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="6056a-299">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="6056a-300">Aşağıdaki örneklerde,. *DLL* dosyaları *. bin* dosya uzantısını kullanacak şekilde yeniden adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6056a-300">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="6056a-301">Windows'da:</span><span class="sxs-lookup"><span data-stu-id="6056a-301">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="6056a-302">Linux veya macOS 'ta:</span><span class="sxs-lookup"><span data-stu-id="6056a-302">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```
   
<span data-ttu-id="6056a-303">*. Bin*' den farklı bir dosya uzantısı kullanmak için, önceki komutlarda *. bin yerine.*</span><span class="sxs-lookup"><span data-stu-id="6056a-303">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="6056a-304">Sıkıştırılmış *blazor. Boot. JSON. gz* ve *blazor.Boot.JSON.br* dosyalarını ele almak için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="6056a-304">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="6056a-305">Sıkıştırılmış *blazor. Boot. JSON. gz* ve *blazor.Boot.JSON.br* dosyalarını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="6056a-305">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="6056a-306">Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="6056a-306">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="6056a-307">Güncelleştirilmiş *blazor. Boot. JSON* dosyasını yeniden sıkıştırın.</span><span class="sxs-lookup"><span data-stu-id="6056a-307">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="6056a-308">Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.</span><span class="sxs-lookup"><span data-stu-id="6056a-308">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="6056a-309">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="6056a-309">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="6056a-310">Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="6056a-310">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="6056a-311">Geri bildirim sağlamak için, [#5477 aspnetcore/sorunlar](https://github.com/dotnet/aspnetcore/issues/5477)' ı ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="6056a-311">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
