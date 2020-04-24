---
title: ASP.NET Core Blazor webassembly 'ı barındırma ve dağıtma
author: guardrex
description: ASP.NET Core, Içerik teslim ağları (CDN Blazor ), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: daaaab360e93de1cf10feec2db21d3acc25920bd
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110882"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="ff556-103">ASP.NET Core Blazor webassembly 'ı barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="ff556-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ff556-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27)ve [ben Adams](https://twitter.com/ben_a_adams)tarafından.</span><span class="sxs-lookup"><span data-stu-id="ff556-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), and [Ben Adams](https://twitter.com/ben_a_adams).</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ff556-105">Webassembly barındırma modeliyle: [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="ff556-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="ff556-106">Blazor Uygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="ff556-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="ff556-107">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="ff556-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="ff556-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="ff556-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="ff556-109">Uygulama Blazor , bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="ff556-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="ff556-110">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ff556-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="ff556-111">Blazor Uygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .net Blazor uygulamayı sunmak için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="ff556-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="ff556-112">Bu strateji [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır. Bu, bir Blazor WEBASSEMBLY uygulamasını bir IIS alt uygulaması olarak barındırma hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="ff556-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="ff556-113">Brotli ön sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="ff556-113">Brotli precompression</span></span>

<span data-ttu-id="ff556-114">Blazor Webassembly uygulaması yayımlandığında, çıkış en yüksek düzeydeki [Brotli sıkıştırma algoritması](https://tools.ietf.org/html/rfc7932) kullanılarak, uygulama boyutunu azaltmak ve çalışma zamanı sıkıştırması gereksinimini kaldırmak için önceden sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="ff556-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="ff556-115">IIS *Web. config* sıkıştırma yapılandırması için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ff556-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="ff556-116">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="ff556-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="ff556-117">Blazor Webassembly uygulamasındaki sayfa bileşenlerine yönelik yönlendirme istekleri, sunucuda, barındırılan bir Blazor uygulamada yönlendirme istekleri kadar basittir.</span><span class="sxs-lookup"><span data-stu-id="ff556-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="ff556-118">İki bileşeni Blazor olan bir webassembly uygulaması düşünün:</span><span class="sxs-lookup"><span data-stu-id="ff556-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="ff556-119">*Main. Razor* &ndash; , uygulamanın köküne yüklenir ve `About` bileşene (`href="About"`) bir bağlantı içerir.</span><span class="sxs-lookup"><span data-stu-id="ff556-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="ff556-120">*. Razor* &ndash; `About` bileşeni hakkında.</span><span class="sxs-lookup"><span data-stu-id="ff556-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="ff556-121">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="ff556-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="ff556-122">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="ff556-122">The browser makes a request.</span></span>
1. <span data-ttu-id="ff556-123">Varsayılan sayfa döndürülür, bu genellikle *index. html*'dir.</span><span class="sxs-lookup"><span data-stu-id="ff556-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="ff556-124">uygulamanın *index. html* önyükleme.</span><span class="sxs-lookup"><span data-stu-id="ff556-124">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="ff556-125">uygulamasının yönlendirici yükleri ve Razor `Main` bileşeni işlenir.</span><span class="sxs-lookup"><span data-stu-id="ff556-125">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="ff556-126">`About` Ana sayfada, Blazor yönlendirici tarayıcıyı `www.contoso.com` Internet 'te için bir istek yapmayı durdurduğundan `About` ve işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="ff556-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="ff556-127">Webassembly uygulamasındaki iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: Istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez. \* Blazor \*</span><span class="sxs-lookup"><span data-stu-id="ff556-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="ff556-128">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="ff556-128">The router handles the requests internally.</span></span>

<span data-ttu-id="ff556-129">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About`, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ff556-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="ff556-130">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ff556-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="ff556-131">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri, fiziksel olarak sunucu üzerinde olmayan kaynakların tüm isteklerini *Dizin. html* sayfasına yeniden yazmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff556-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="ff556-132">*İndex. html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="ff556-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="ff556-133">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayınlanan *Web. config* dosyasıyla birlikte kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff556-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="ff556-134">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ff556-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="ff556-135">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="ff556-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="ff556-136">*Barındırılan dağıtım* , Blazor webassembly uygulamasını web sunucusunda çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılara sunar.</span><span class="sxs-lookup"><span data-stu-id="ff556-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="ff556-137">İstemci Blazor weelsembly uygulaması, sunucu UYGULAMASıNıN */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="ff556-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ff556-138">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="ff556-138">The two apps are deployed together.</span></span> <span data-ttu-id="ff556-139">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ff556-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ff556-140">Barındırılan bir dağıtım için Visual Studio, **barındırılan** seçenek seçili`-ho|--hosted` olarak ( [DotNet yeni](/dotnet/core/tools/dotnet-new) komut kullanılırken`blazorwasm` şablon), \*\* Blazor webassembly uygulama\*\* projesi şablonunu ( `dotnet new` komut kullanılırken) içerir.</span><span class="sxs-lookup"><span data-stu-id="ff556-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="ff556-141">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index>..</span><span class="sxs-lookup"><span data-stu-id="ff556-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ff556-142">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs>..</span><span class="sxs-lookup"><span data-stu-id="ff556-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="ff556-143">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="ff556-143">Standalone deployment</span></span>

<span data-ttu-id="ff556-144">*Tek başına dağıtım* , Blazor webassembly uygulamasına doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="ff556-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="ff556-145">Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.</span><span class="sxs-lookup"><span data-stu-id="ff556-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="ff556-146">Tek başına dağıtım varlıkları */BIN/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="ff556-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="ff556-147">IIS</span><span class="sxs-lookup"><span data-stu-id="ff556-147">IIS</span></span>

<span data-ttu-id="ff556-148">IIS, uygulamalar için Blazor özellikli bir statik dosya sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="ff556-148">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="ff556-149">IIS 'yi barındıracak Blazorşekilde yapılandırmak için bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ff556-149">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="ff556-150">Yayımlanan varlıklar */BIN/Release/{Target Framework}/Publish* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ff556-150">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ff556-151">Web sunucusunda veya barındırma hizmetinde *Yayımlama* klasörünün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="ff556-151">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="ff556-152">Web. config</span><span class="sxs-lookup"><span data-stu-id="ff556-152">web.config</span></span>

<span data-ttu-id="ff556-153">Bir Blazor proje yayımlandığında, aşağıdaki IIS yapılandırmasıyla bir *Web. config* dosyası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ff556-153">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="ff556-154">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ff556-154">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="ff556-155">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="ff556-155">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="ff556-156">*. JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="ff556-156">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="ff556-157">&ndash; *.*`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="ff556-157">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="ff556-158">*. WOFF* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ff556-158">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="ff556-159">*. woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ff556-159">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="ff556-160">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="ff556-160">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="ff556-161">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="ff556-161">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="ff556-162">Uygulamanın statik varlıklarının bulunduğu alt dizini (*Wwwroot/{Path istenen}*) sunar.</span><span class="sxs-lookup"><span data-stu-id="ff556-162">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="ff556-163">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe (*Wwwroot/index.html*) uygulamanın varsayılan belgesine yeniden YÖNLENDIRILMESI için Spa geri dönüş yönlendirmesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff556-163">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="ff556-164">Özel Web. config kullanma</span><span class="sxs-lookup"><span data-stu-id="ff556-164">Use a custom web.config</span></span>

<span data-ttu-id="ff556-165">Özel *Web. config* dosyasını kullanmak için, özel *Web. config* dosyasını proje klasörünün köküne yerleştirin ve projeyi yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-165">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="ff556-166">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="ff556-166">Install the URL Rewrite Module</span></span>

<span data-ttu-id="ff556-167">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ff556-167">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="ff556-168">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="ff556-168">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="ff556-169">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ff556-169">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="ff556-170">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="ff556-170">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="ff556-171">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="ff556-171">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="ff556-172">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="ff556-172">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="ff556-173">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="ff556-173">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="ff556-174">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-174">Copy the installer to the server.</span></span> <span data-ttu-id="ff556-175">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ff556-175">Run the installer.</span></span> <span data-ttu-id="ff556-176">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ff556-176">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="ff556-177">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ff556-177">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="ff556-178">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff556-178">Configure the website</span></span>

<span data-ttu-id="ff556-179">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-179">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="ff556-180">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="ff556-180">The folder contains:</span></span>

* <span data-ttu-id="ff556-181">Gerekli yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS 'nin Web sitesini yapılandırmak için kullandığı *Web. config* dosyası.</span><span class="sxs-lookup"><span data-stu-id="ff556-181">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="ff556-182">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="ff556-182">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="ff556-183">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="ff556-183">Host as an IIS sub-app</span></span>

<span data-ttu-id="ff556-184">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="ff556-184">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="ff556-185">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="ff556-185">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="ff556-186">Dosyaya bir Blazor `<handlers>` bölüm ekleyerek uygulamanın yayınlanan *Web. config* dosyasındaki işleyiciyi kaldırın:</span><span class="sxs-lookup"><span data-stu-id="ff556-186">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="ff556-187">Şu `<system.webServer>` şekilde `<location>` `inheritInChildApplications` ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın: `false`</span><span class="sxs-lookup"><span data-stu-id="ff556-187">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="ff556-188">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:host-and-deploy/blazor/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ff556-188">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="ff556-189">Uygulamanın *index. html* dosyasındaki uygulama temel yolunu, IIS 'de alt uygulamayı YAPıLANDıRıRKEN kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="ff556-190">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="ff556-190">Brotli and Gzip compression</span></span>

<span data-ttu-id="ff556-191">IIS, *Web. config* aracılığıyla Brotli veya gzip sıkıştırılan Blazor varlıkları sunacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff556-191">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="ff556-192">Örnek bir yapılandırma için bkz. [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="ff556-192">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="ff556-193">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="ff556-193">Troubleshooting</span></span>

<span data-ttu-id="ff556-194">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="ff556-195">Modül yüklü olmadığında, *Web. config* dosyası IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="ff556-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="ff556-196">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet Blazorolarak yüklemesini engeller.</span><span class="sxs-lookup"><span data-stu-id="ff556-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="ff556-197">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için <xref:test/troubleshoot-azure-iis>bkz..</span><span class="sxs-lookup"><span data-stu-id="ff556-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="ff556-198">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="ff556-198">Azure Storage</span></span>

<span data-ttu-id="ff556-199">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff556-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="ff556-200">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ff556-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="ff556-201">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="ff556-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="ff556-202">**Dizin belgesi adını** olarak `index.html`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="ff556-203">**Hata belge yolunu** olarak `index.html`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="ff556-204">Razor bileşenleri ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="ff556-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="ff556-205">Blazor Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında, blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna**yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="ff556-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="ff556-206">*İndex. html* blobu döndürülür ve Blazor yönlendirici yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="ff556-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="ff556-207">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="ff556-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="ff556-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="ff556-208">Nginx</span></span>

<span data-ttu-id="ff556-209">Aşağıdaki *NGINX. conf* dosyası, NGINX 'in, disk üzerinde karşılık gelen bir dosyayı bulamadığı her seferinde *index. html* dosyasını göndermek üzere nasıl yapılandırılacağını gösterecek şekilde basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff556-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="ff556-210">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="ff556-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="ff556-211">Docker 'da NGINX</span><span class="sxs-lookup"><span data-stu-id="ff556-211">Nginx in Docker</span></span>

<span data-ttu-id="ff556-212">NGINX kullanarak Docker 'da barındırmak Blazor Için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="ff556-213">Dockerfile dosyasını, *NGINX. config* dosyasını kapsayıcıya kopyalamak için güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="ff556-214">Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ff556-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="ff556-215">Apache</span><span class="sxs-lookup"><span data-stu-id="ff556-215">Apache</span></span>

<span data-ttu-id="ff556-216">Cenelsembly Blazor uygulamasını CentOS 7 veya sonraki bir sürüme dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="ff556-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="ff556-217">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff556-217">Create the Apache configuration file.</span></span> <span data-ttu-id="ff556-218">Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="ff556-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="ff556-219">Apache yapılandırma dosyasını, CentOS 7 `/etc/httpd/conf.d/` ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="ff556-220">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin (yapılandırma dosyasında belirtilen `DocumentRoot` konum).</span><span class="sxs-lookup"><span data-stu-id="ff556-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="ff556-221">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="ff556-221">Restart the Apache service.</span></span>

<span data-ttu-id="ff556-222">Daha fazla bilgi için bkz. [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="ff556-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="ff556-223">GitHub sayfaları</span><span class="sxs-lookup"><span data-stu-id="ff556-223">GitHub Pages</span></span>

<span data-ttu-id="ff556-224">URL yeniden işlemesini işlemek için, isteği *index. html* sayfasına yönlendirmeyi işleyen bir betiği olan bir *404. html* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ff556-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="ff556-225">Topluluk tarafından sunulan örnek bir uygulama için bkz. [GitHub sayfaları Için tek sayfalı uygulamalar](https://spa-github-pages.rafrex.com/) ([GitHub üzerinde rafrex/Spa-GitHub-Pages](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="ff556-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="ff556-226">Topluluk yaklaşımını kullanan bir örnek, GitHub ([canlı site](https://blazor-demo.github.io/)) [üzerinde blazor-demo/blazor-demo. GitHub. IO](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir.</span><span class="sxs-lookup"><span data-stu-id="ff556-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="ff556-227">Bir kuruluş sitesi yerine bir proje sitesi kullanırken `<base>` etiketi *index. html*dosyasına ekleyin veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="ff556-228">`href` Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin, `my-repository/`) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ff556-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ff556-229">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="ff556-229">Host configuration values</span></span>

<span data-ttu-id="ff556-230">Webassembly uygulamaları, geliştirme ortamındaki çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="ff556-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="ff556-231">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="ff556-231">Content root</span></span>

<span data-ttu-id="ff556-232">`--contentroot` Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ff556-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="ff556-233">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="ff556-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="ff556-234">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ff556-235">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ff556-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="ff556-236">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ff556-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ff556-237">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile `dotnet run`bir komut isteminden çalıştırıldığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff556-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="ff556-238">Visual Studio 'da, **Özellikler** > **hata ayıklama** > **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ff556-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ff556-239">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ff556-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="ff556-240">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="ff556-240">Path base</span></span>

<span data-ttu-id="ff556-241">Bağımsız `--pathbase` değişkeni, kök olmayan GÖRELI bir URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket `href` , hazırlama ve üretim `/` için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="ff556-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="ff556-242">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="ff556-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="ff556-243">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ff556-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ff556-244">Etiketinde belirtilen `href` yolun aksine,`/` `--pathbase` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin. `<base>`</span><span class="sxs-lookup"><span data-stu-id="ff556-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="ff556-245">`<base>` Etikette uygulama temel yolu (sondaki eğik çizgi içeriyorsa) olarak `<base href="/CoolApp/">` sağlanmışsa, komut satırı bağımsız değişken değerini (sondaki eğik çizgi yok) olarak `--pathbase=/CoolApp` geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="ff556-246">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ff556-247">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ff556-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="ff556-248">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ff556-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ff556-249">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile `dotnet run`bir komut isteminden çalıştırırken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff556-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="ff556-250">Visual Studio 'da, **Özellikler** > **hata ayıklama** > **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ff556-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ff556-251">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ff556-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="ff556-252">URL’ler</span><span class="sxs-lookup"><span data-stu-id="ff556-252">URLs</span></span>

<span data-ttu-id="ff556-253">`--urls` Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve PROTOKOLLERLE IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ff556-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="ff556-254">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff556-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ff556-255">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ff556-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="ff556-256">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ff556-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ff556-257">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile `dotnet run`bir komut isteminden çalıştırırken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff556-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="ff556-258">Visual Studio 'da, **Özellikler** > **hata ayıklama** > **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ff556-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ff556-259">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ff556-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="ff556-260">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff556-260">Configure the Linker</span></span>

Blazor<span data-ttu-id="ff556-261">çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="ff556-261"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="ff556-262">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="ff556-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
