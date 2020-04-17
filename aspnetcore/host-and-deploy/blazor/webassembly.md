---
title: Core Blazor WebAssemblyASP.NET ana bilgisayar ve dağıtma
author: guardrex
description: ASP.NET Core, Content Blazor Delivery Networks (CDN), dosya sunucuları ve GitHub Sayfalarını kullanarak bir uygulamayı nasıl barındıracağınızı ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f3508144f1e472ee906a35e427fc57f536008ab6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488864"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="d9f1d-103">Core Blazor WebAssemblyASP.NET ana bilgisayar ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="d9f1d-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="d9f1d-104">Luke [Latham](https://github.com/guardrex)tarafından , [Rainer Stropek](https://www.timecockpit.com), ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d9f1d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d9f1d-105">[ Blazor WebAssembly barındırma modeli](xref:blazor/hosting-models#blazor-webassembly)ile:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="d9f1d-106">Uygulama, Blazor bağımlılıkları ve .NET çalışma süresi tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="d9f1d-107">Uygulama doğrudan tarayıcı UI iş parçacığı üzerinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="d9f1d-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="d9f1d-109">Uygulama, Blazor ASP.NET Core uygulaması tarafından sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="d9f1d-110">Bu strateji, [ASP.NET Core bölümüyle Barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="d9f1d-111">Uygulama, Blazor .NET'in uygulamaya hizmet vermek için kullanılmadığı statik bir Blazor barındırma web sunucusuna veya hizmetine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="d9f1d-112">Bu strateji, Bir [Standalone deployment](#standalone-deployment) Blazor WebAssembly uygulamasını IIS alt uygulaması olarak barındırma hakkında bilgi içeren Bağımsız dağıtım bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="d9f1d-113">Brotli ön kompresyon</span><span class="sxs-lookup"><span data-stu-id="d9f1d-113">Brotli precompression</span></span>

<span data-ttu-id="d9f1d-114">Bir Blazor WebAssembly uygulaması yayımlandığında, çıktı, uygulama boyutunu küçültmek ve çalışma zamanı sıkıştırma gereksinimini ortadan kaldırmak için [brotli sıkıştırma algoritması](https://tools.ietf.org/html/rfc7932) kullanılarak en üst düzeyde sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="d9f1d-115">Doğru yönlendirme için URL'leri yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="d9f1d-115">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="d9f1d-116">Bir Blazor WebAssembly uygulamasındaki sayfa bileşenleri isteklerini yönlendirme, barındırılan bir Blazor sunucudaki yönlendirme istekleri kadar basit değildir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-116">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="d9f1d-117">İki Blazor bileşenden bir WebAssembly uygulaması düşünün:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-117">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="d9f1d-118">*Main.razor* &ndash; Yükler uygulamanın kökünde ve `About` bileşene bir`href="About"`bağlantı içerir ( ).</span><span class="sxs-lookup"><span data-stu-id="d9f1d-118">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="d9f1d-119">*Jilet* &ndash; `About` bileşeni hakkında.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-119">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="d9f1d-120">Uygulamanın varsayılan belgesi tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin,): `https://www.contoso.com/`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-120">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="d9f1d-121">Tarayıcı bir istekte bulundu.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-121">The browser makes a request.</span></span>
1. <span data-ttu-id="d9f1d-122">Varsayılan sayfa döndürülür, genellikle *index.html*.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-122">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="d9f1d-123">*index.html* uygulamayı bootstraps.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-123">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="d9f1d-124">''nin yönlendirici yükleri `Main` ve Razor bileşeni işlenir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-124">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="d9f1d-125">`About` Ana sayfada, bileşenin bağlantısını seçmek istemcide çalışır, Blazor çünkü yönlendirici tarayıcının Internet'te istekte `www.contoso.com` `About` bulunmasını durdurur `About` ve işlenen bileşenin kendisi için hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-125">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="d9f1d-126">\*WebAssembly uygulamasındaki Blazor \* dahili uç noktalara yönelik tüm istekler aynı şekilde çalışır: İstekler, Internet'te sunucu tarafından barındırılan kaynaklara tarayıcı tabanlı istekleri tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-126">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="d9f1d-127">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-127">The router handles the requests internally.</span></span>

<span data-ttu-id="d9f1d-128">Tarayıcının adres çubuğu kullanılarak bir istek `www.contoso.com/About`yapılırsa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-128">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="d9f1d-129">Uygulamanın Internet ana bilgisayarında böyle bir kaynak bulunmadığından, *404 - Bulunamadı* yanıtı döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-129">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="d9f1d-130">Tarayıcılar istemci tarafı sayfaları için Internet tabanlı ana bilgisayarlara istekte bulunduğundan, web sunucuları ve barındırma hizmetleri sunucudaki kaynaklara yönelik tüm istekleri ni fiziksel olarak *index.html* sayfasına yeniden yazmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-130">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="d9f1d-131">*index.html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi devreye girer ve doğru kaynakla yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-131">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="d9f1d-132">Bir IIS sunucusuna dağıtırken, uygulamanın yayınlanan *web.config* dosyasıyla URL Yeniden Yazma Modül'üni kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-132">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="d9f1d-133">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-133">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="d9f1d-134">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="d9f1d-134">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="d9f1d-135">*Barındırılan dağıtım,* WebAssembly uygulamasına Blazor bir web sunucusunda çalışan bir ASP.NET Core [uygulamasından](xref:index) tarayıcılara hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-135">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="d9f1d-136">İstemci Blazor WebAssembly uygulaması sunucu uygulamasının */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe, sunucu uygulamasının diğer statik web varlıklarıyla birlikte yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-136">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="d9f1d-137">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-137">The two apps are deployed together.</span></span> <span data-ttu-id="d9f1d-138">ASP.NET Core uygulaması barındırma yeteneğine sahip bir web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-138">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="d9f1d-139">Barındırılan`blazorwasm` bir dağıtım için Visual Studio,`-ho|--hosted` `dotnet new` \*\* Blazor WebAssembly App\*\* proje şablonunu [(dotnet yeni](/dotnet/core/tools/dotnet-new) komutunu kullanırken şablon) içerir ve **Barındırılan** seçeneği (komutu kullanırken) seçilir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-139">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="d9f1d-140">ASP.NET Core uygulaması barındırma ve dağıtımı <xref:host-and-deploy/index>hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-140">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="d9f1d-141">Azure Uygulama Hizmetine dağıtım hakkında <xref:tutorials/publish-to-azure-webapp-using-vs>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-141">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="d9f1d-142">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="d9f1d-142">Standalone deployment</span></span>

<span data-ttu-id="d9f1d-143">*Bağımsız bir dağıtım,* WebAssembly uygulamasına Blazor doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-143">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="d9f1d-144">Herhangi bir statik dosya sunucusu Blazor uygulamaya hizmet verebiliyor.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-144">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="d9f1d-145">Bağımsız dağıtım varlıkları */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-145">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="d9f1d-146">IIS</span><span class="sxs-lookup"><span data-stu-id="d9f1d-146">IIS</span></span>

<span data-ttu-id="d9f1d-147">IIS uygulamalar için Blazor yetenekli bir statik dosya sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-147">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="d9f1d-148">IIS'yi barındıracak Blazorşekilde yapılandırmak için [bkz.](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)</span><span class="sxs-lookup"><span data-stu-id="d9f1d-148">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="d9f1d-149">Yayınlanan varlıklar */bin/Release/{TARGET FRAMEWORK}/publish* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-149">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="d9f1d-150">*Yayımlama* klasörünün içeriğini web sunucusunda veya barındırma hizmetinde barındırın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-150">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="d9f1d-151">Config</span><span class="sxs-lookup"><span data-stu-id="d9f1d-151">web.config</span></span>

<span data-ttu-id="d9f1d-152">Bir Blazor proje yayımlandığında, aşağıdaki IIS yapılandırmasıyla bir *web.config* dosyası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-152">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="d9f1d-153">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-153">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="d9f1d-154">*.dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-154">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="d9f1d-155">*.json* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-155">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="d9f1d-156">*.wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-156">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="d9f1d-157">*.woff* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-157">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="d9f1d-158">*.woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-158">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="d9f1d-159">Aşağıdaki MIME türleri için HTTP sıkıştırma etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-159">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="d9f1d-160">URL Yeniden Yazma Modülü kuralları belirlenir:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-160">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="d9f1d-161">Uygulamanın statik varlıklarının bulunduğu alt dizine hizmet *(wwwroot/{PATH REQUESTed}*).</span><span class="sxs-lookup"><span data-stu-id="d9f1d-161">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="d9f1d-162">Dosya dışı varlıklara yönelik isteklerin, statik varlıklar klasöründe *(wwwroot/index.html)* uygulamanın varsayılan belgesine yönlendirilmesi için SPA geri dönüş yönlendirmesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-162">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="d9f1d-163">Özel bir web.config kullanın</span><span class="sxs-lookup"><span data-stu-id="d9f1d-163">Use a custom web.config</span></span>

<span data-ttu-id="d9f1d-164">Özel bir *web.config* dosyası kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-164">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="d9f1d-165">Özel *web.config* dosyasını proje klasörünün köküne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-165">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="d9f1d-166">Proje dosyasına aşağıdaki hedefi ekleyin (*.csproj):*</span><span class="sxs-lookup"><span data-stu-id="d9f1d-166">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="d9f1d-167">IIS'e `<IsWebConfigTransformDisabled>` `true` dağıtılan ASP.NET Core uygulamaları için Blazor olduğu [gibi,](xref:host-and-deploy/iis/index#webconfig-file)WebAssembly uygulamalarında da desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-167">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="d9f1d-168">Daha fazla bilgi için bkz: [Özel Blazor WASM web.config (dotnet/aspnetcore #20569) sağlamak için gereken kopya hedef.](https://github.com/dotnet/aspnetcore/issues/20569)</span><span class="sxs-lookup"><span data-stu-id="d9f1d-168">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="d9f1d-169">URL Yeniden Yazma Modül'e yükleme</span><span class="sxs-lookup"><span data-stu-id="d9f1d-169">Install the URL Rewrite Module</span></span>

<span data-ttu-id="d9f1d-170">[URL Yeniden Yazma Modülü](https://www.iis.net/downloads/microsoft/url-rewrite) URL'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-170">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="d9f1d-171">Modül varsayılan olarak yüklü değildir ve Web Sunucusu (IIS) rol hizmeti özelliği olarak yüklemek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-171">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="d9f1d-172">Modül IIS web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-172">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="d9f1d-173">Modülü yüklemek için Web Platform Yükleyicisini kullanın:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-173">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="d9f1d-174">Yerel olarak, [URL Yeniden Yazma Modülü indirme sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-174">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="d9f1d-175">İngilizce sürüm için **WebPI** yükleyicisini indirmek için WebPI'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-175">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="d9f1d-176">Diğer diller için, yükleyiciyi indirmek için sunucu (x86/x64) için uygun mimariyi seçin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-176">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="d9f1d-177">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-177">Copy the installer to the server.</span></span> <span data-ttu-id="d9f1d-178">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-178">Run the installer.</span></span> <span data-ttu-id="d9f1d-179">**Yükle** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-179">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="d9f1d-180">Yükleme tamamlandıktan sonra sunucu yeniden başlatması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-180">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="d9f1d-181">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d9f1d-181">Configure the website</span></span>

<span data-ttu-id="d9f1d-182">Web sitesinin **Fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-182">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="d9f1d-183">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-183">The folder contains:</span></span>

* <span data-ttu-id="d9f1d-184">IIS'nin web sitesini yapılandırmak için kullandığı *web.config* dosyası, gerekli yönlendirme kuralları ve dosya içeriği türleri de dahil olmak üzere.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-184">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="d9f1d-185">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-185">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="d9f1d-186">IIS alt uygulaması olarak ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="d9f1d-186">Host as an IIS sub-app</span></span>

<span data-ttu-id="d9f1d-187">Bağımsız bir uygulama IIS alt uygulaması olarak barındırılırsa, aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-187">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="d9f1d-188">Devralınan ASP.NET Çekirdek Modülü işleyicisini devre dışı bırak.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-188">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="d9f1d-189">Dosyaya bir `<handlers>` bölüm Blazor ekleyerek uygulamanın yayınlanan *web.config* dosyasındaki işleyiciyi kaldırın:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-189">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="d9f1d-190">`inheritInChildApplications` Set `false`ile bir `<system.webServer>` `<location>` öğe kullanarak kök (üst) uygulama bölümünün devralma devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-190">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="d9f1d-191">Uygulamanın [temel yolunu yapılandırmaya](xref:host-and-deploy/blazor/index#app-base-path)ek olarak işleyicinin kaldırılması veya devralmanın devre dışı bırakılması gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-191">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="d9f1d-192">Uygulamanın *index.html* dosyasındaki uygulama temel yolunu, Alt Uygulamayı IIS'de yapılandırırken kullanılan IIS diğer adıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-192">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="d9f1d-193">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="d9f1d-193">Troubleshooting</span></span>

<span data-ttu-id="d9f1d-194">*500 - Internal Server Hatası* alınırsa ve IIS Yöneticisi web sitesinin yapılandırmasına erişmeye çalışırken hata lar atarsa, URL Yeniden Yazma Modülü'nün yüklü olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="d9f1d-195">Modül yüklenmediği zaman, *web.config* dosyası IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="d9f1d-196">Bu, IIS Yöneticisi'nin web sitesinin yapılandırmasını ve Blazorweb sitesinin statik dosyalarını yüklemesini engeller.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="d9f1d-197">IIS'deki sorun giderme dağıtımları <xref:test/troubleshoot-azure-iis>hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="d9f1d-198">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="d9f1d-198">Azure Storage</span></span>

<span data-ttu-id="d9f1d-199">[Azure Depolama](/azure/storage/) statik dosya Blazor barındırma sunucusuz uygulama barındırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="d9f1d-200">Özel alan adları, Azure İçerik Dağıtım Ağı (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="d9f1d-201">Bir depolama hesabında statik web sitesi barındırma için blob hizmeti etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="d9f1d-202">**Dizin belge** adını `index.html`' a ayarlayın</span><span class="sxs-lookup"><span data-stu-id="d9f1d-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="d9f1d-203">Hata **belge yolunu** `index.html`' n için ayarlama</span><span class="sxs-lookup"><span data-stu-id="d9f1d-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="d9f1d-204">Jilet bileşenleri ve diğer dosya dışı uç noktalar, blob hizmeti tarafından depolanan statik içerikteki fiziksel yollarda bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="d9f1d-205">Bu kaynaklardan biri için yönlendiricinin Blazor işlemesi gereken bir istek alındığı zaman, blob hizmeti tarafından oluşturulan *404 - Bulunamadı* hatası isteği Hata belge **yoluna**yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="d9f1d-206">*Index.html* blob döndürülür ve Blazor yönlendirici yükler ve yol işler.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="d9f1d-207">Daha fazla bilgi için [Azure Depolama'da barındırılmayan Statik web sitesine](/azure/storage/blobs/storage-blob-static-website)bakın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="d9f1d-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="d9f1d-208">Nginx</span></span>

<span data-ttu-id="d9f1d-209">Aşağıdaki *nginx.conf* dosyası, diskte karşılık gelen bir dosya bulamadığında *index.html* dosyasını göndermek için Nginx'in nasıl yapılandırılacağı gösterilebilir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="d9f1d-210">Nginx web sunucusu yapılandırması hakkında daha fazla bilgi için [NGINX Plus ve NGINX Configuration Files oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)bilgisine bakın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="d9f1d-211">Docker içinde Nginx</span><span class="sxs-lookup"><span data-stu-id="d9f1d-211">Nginx in Docker</span></span>

<span data-ttu-id="d9f1d-212">Nginx kullanarak Docker'da barındırmak Blazor için, Alp tabanlı Nginx görüntüsünü kullanmak üzere Dockerfile'ı kurun.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="d9f1d-213">*Nginx.config* dosyasını kapsayıcıya kopyalamak için Dockerdosyasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="d9f1d-214">Aşağıdaki örnekte gösterildiği gibi Dockerfile'a bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="d9f1d-215">Apache</span><span class="sxs-lookup"><span data-stu-id="d9f1d-215">Apache</span></span>

<span data-ttu-id="d9f1d-216">Bir Blazor WebAssembly uygulamasını CentOS 7 veya sonraki lere dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="d9f1d-217">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-217">Create the Apache configuration file.</span></span> <span data-ttu-id="d9f1d-218">Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp.config):*</span><span class="sxs-lookup"><span data-stu-id="d9f1d-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="d9f1d-219">Apache yapılandırma dosyasını `/etc/httpd/conf.d/` CentOS 7'deki varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="d9f1d-220">Uygulamanın dosyalarını `/var/www/blazorapp` dizine (yapılandırma `DocumentRoot` dosyasında belirtilen konum) yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="d9f1d-221">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-221">Restart the Apache service.</span></span>

<span data-ttu-id="d9f1d-222">Daha fazla bilgi için [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)bakın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="d9f1d-223">GitHub Sayfaları</span><span class="sxs-lookup"><span data-stu-id="d9f1d-223">GitHub Pages</span></span>

<span data-ttu-id="d9f1d-224">URL yeniden yazma işlemlerini işlemek için, isteği *index.html* sayfasına yönlendirmeyi işleyen bir komut dosyası içeren *bir 404.html* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="d9f1d-225">Topluluk tarafından sağlanan örnek bir uygulama [için, GitHub Sayfaları için Tek Sayfa Uygulamaları](https://spa-github-pages.rafrex.com/) [(GitHub'daki rafrex/spa-github sayfaları)](https://github.com/rafrex/spa-github-pages#readme)sayfasına bakın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="d9f1d-226">Topluluk yaklaşımını kullanan bir örnek [github'daki blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir ([canlı site).](https://blazor-demo.github.io/)</span><span class="sxs-lookup"><span data-stu-id="d9f1d-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="d9f1d-227">Kuruluş sitesi yerine proje sitesi kullanırken *index.html*etiketini `<base>` ekleyin veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="d9f1d-228">`href` Öznitelik değerini GitHub deposu adına sondalı bir eğik çizgiyle `my-repository/`ayarlama (örneğin, .</span><span class="sxs-lookup"><span data-stu-id="d9f1d-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d9f1d-229">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="d9f1d-229">Host configuration values</span></span>

<span data-ttu-id="d9f1d-230">WebAssembly uygulamaları, geliştirme ortamında çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="d9f1d-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="d9f1d-231">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="d9f1d-231">Content root</span></span>

<span data-ttu-id="d9f1d-232">Bağımsız `--contentroot` değişken, uygulamanın içerik dosyalarını[(içerik kökü)](xref:fundamentals/index#content-root)içeren dizin için mutlak yolu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="d9f1d-233">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kökü yolu verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="d9f1d-234">Uygulamayı bir komut istemiyle yerel olarak çalıştırırken bağımsız değişkeni geçirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="d9f1d-235">Uygulamanın dizininden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="d9f1d-236">**Uygulamanın iIS Express** profilindeki *launchSettings.json* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="d9f1d-237">Bu ayar, uygulama Visual Studio Debugger ile çalıştırıldığında ve `dotnet run`bir komut isteminden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="d9f1d-238">Visual Studio'da, **Özellikler** > **Hata Ayıklama** > Uygulaması bağımsız**değişkenindeki**bağımsız değişkeni belirtin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="d9f1d-239">Visual Studio özellik sayfasındaki bağımsız değişkeni ayarlamak, bağımsız değişkeni *launchSettings.json* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="d9f1d-240">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="d9f1d-240">Path base</span></span>

<span data-ttu-id="d9f1d-241">Bağımsız `--pathbase` değişken, kök olmayan göreli URL yolu ile yerel olarak çalışan `<base>` `href` bir uygulama için uygulama `/` temel yolunu ayarlar (etiket, hazırlama ve üretim dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="d9f1d-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="d9f1d-242">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı dır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="d9f1d-243">Daha fazla bilgi için [Uygulama tabanı yoluna](xref:host-and-deploy/blazor/index#app-base-path)bakın.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d9f1d-244">Etikete sağlanan yolun aksine, bağımsız değişken değerini geçerken`/`bir çizgi () eklemeyin. `--pathbase` `href` `<base>`</span><span class="sxs-lookup"><span data-stu-id="d9f1d-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="d9f1d-245">Uygulama temel yolu `<base>` etikette (sondaki `<base href="/CoolApp/">` eğik çizgi içerir) sağlanırsa, `--pathbase=/CoolApp` komut satırı bağımsız değişken değerini (izleme çizgisini yok) olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="d9f1d-246">Uygulamayı bir komut istemiyle yerel olarak çalıştırırken bağımsız değişkeni geçirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="d9f1d-247">Uygulamanın dizininden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="d9f1d-248">**Uygulamanın iIS Express** profilindeki *launchSettings.json* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="d9f1d-249">Bu ayar, Visual Studio Debugger ile uygulamayı çalıştırırken ve `dotnet run`bir komut isteminden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="d9f1d-250">Visual Studio'da, **Özellikler** > **Hata Ayıklama** > Uygulaması bağımsız**değişkenindeki**bağımsız değişkeni belirtin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="d9f1d-251">Visual Studio özellik sayfasındaki bağımsız değişkeni ayarlamak, bağımsız değişkeni *launchSettings.json* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="d9f1d-252">URL’ler</span><span class="sxs-lookup"><span data-stu-id="d9f1d-252">URLs</span></span>

<span data-ttu-id="d9f1d-253">Bağımsız `--urls` değişken, istekleri dinleyecek bağlantı noktaları ve protokolleri olan IP adreslerini veya ana bilgisayar adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="d9f1d-254">Uygulamayı bir komut istemiyle yerel olarak çalıştırırken bağımsız değişkeni geçirin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="d9f1d-255">Uygulamanın dizininden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d9f1d-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="d9f1d-256">**Uygulamanın iIS Express** profilindeki *launchSettings.json* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="d9f1d-257">Bu ayar, Visual Studio Debugger ile uygulamayı çalıştırırken ve `dotnet run`bir komut isteminden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="d9f1d-258">Visual Studio'da, **Özellikler** > **Hata Ayıklama** > Uygulaması bağımsız**değişkenindeki**bağımsız değişkeni belirtin.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="d9f1d-259">Visual Studio özellik sayfasındaki bağımsız değişkeni ayarlamak, bağımsız değişkeni *launchSettings.json* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="d9f1d-260">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d9f1d-260">Configure the Linker</span></span>

Blazor<span data-ttu-id="d9f1d-261">Çıktı derlemelerinden gereksiz IL'yi kaldırmak için her Sürüm yapısına ara dil (IL) bağlama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-261"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="d9f1d-262">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="d9f1d-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
