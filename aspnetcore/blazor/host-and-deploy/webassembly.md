---
title: ASP.NET Core webassembly 'ı barındırma ve dağıtma Blazor
author: guardrex
description: BlazorASP.NET Core, Içerik teslim ağları (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 4f672c5117beeb09914e802012f0970389fea47f
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103811"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="28801-103">ASP.NET Core webassembly 'ı barındırma ve dağıtma Blazor</span><span class="sxs-lookup"><span data-stu-id="28801-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="28801-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından</span><span class="sxs-lookup"><span data-stu-id="28801-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="28801-105">[ Blazor Webassembly barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="28801-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="28801-106">BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="28801-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="28801-107">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="28801-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="28801-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="28801-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="28801-109">BlazorUygulama, bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="28801-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="28801-110">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="28801-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="28801-111">BlazorUygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz Blazor .</span><span class="sxs-lookup"><span data-stu-id="28801-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="28801-112">Bu strateji [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır. Bu, bir Blazor WEBASSEMBLY uygulamasını bir IIS alt uygulaması olarak barındırma hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="28801-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="28801-113">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="28801-113">Compression</span></span>

<span data-ttu-id="28801-114">Bir Blazor weelsembly uygulaması yayımlandığında, çıkış sırasında, uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="28801-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="28801-115">Aşağıdaki sıkıştırma algoritmaları kullanılır:</span><span class="sxs-lookup"><span data-stu-id="28801-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="28801-116">[Brotli](https://tools.ietf.org/html/rfc7932) (en yüksek düzey)</span><span class="sxs-lookup"><span data-stu-id="28801-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="28801-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="28801-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="28801-118">, uygun sıkıştırılmış dosyaları sunacak ana bilgisayarı kullanır.</span><span class="sxs-lookup"><span data-stu-id="28801-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="28801-119">ASP.NET Core barındırılan bir proje kullanırken, konak proje içerik anlaşması yapabilir ve statik olarak sıkıştırılmış dosyalara hizmet verebilir.</span><span class="sxs-lookup"><span data-stu-id="28801-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="28801-120">BlazorWebassembly tek başına uygulamasını barındırırken, statik olarak sıkıştırılmış dosyaların sunulmasını sağlamak için ek çalışma gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="28801-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="28801-121">IIS *web.config* sıkıştırma yapılandırması için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="28801-121">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="28801-122">GitHub sayfaları gibi statik olarak sıkıştırılmış dosya içeriği anlaşmasını desteklemeyen statik barındırma çözümlerinde barındırırken, Brotli sıkıştırılan dosyaları getirmek ve kodunu çözmek üzere uygulamayı yapılandırmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="28801-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="28801-123">Uygulamadaki [Google/Brotli GitHub deposundan](https://github.com/google/brotli/) Brotli kod çözücüsüne başvurun.</span><span class="sxs-lookup"><span data-stu-id="28801-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="28801-124">Kod çözücüyü kullanmak için uygulamayı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="28801-124">Update the app to use the decoder.</span></span> <span data-ttu-id="28801-125">`<body>` *Wwwroot/index.html* içindeki kapatma etiketinin içindeki biçimlendirmeyi aşağıdaki gibi değiştirin:</span><span class="sxs-lookup"><span data-stu-id="28801-125">Change the markup inside the the closing `<body>` tag in *wwwroot/index.html* to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
          'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
          { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
  </script>
  ```
   
<span data-ttu-id="28801-126">Sıkıştırmayı devre dışı bırakmak için `BlazorEnableCompression` uygulamanın proje dosyasına MSBuild özelliğini ekleyin ve değeri şu şekilde ayarlayın `false` :</span><span class="sxs-lookup"><span data-stu-id="28801-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="28801-127">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="28801-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="28801-128">Webassembly uygulamasındaki sayfa bileşenlerine yönelik yönlendirme istekleri Blazor Blazor , sunucuda, barındırılan bir uygulamada yönlendirme istekleri kadar basittir.</span><span class="sxs-lookup"><span data-stu-id="28801-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="28801-129">Blazorİki bileşeni olan bir webassembly uygulaması düşünün:</span><span class="sxs-lookup"><span data-stu-id="28801-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="28801-130">*Main. Razor*: uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .</span><span class="sxs-lookup"><span data-stu-id="28801-130">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="28801-131">*. Razor*: `About` bileşeni hakkında.</span><span class="sxs-lookup"><span data-stu-id="28801-131">*About.razor*: `About` component.</span></span>

<span data-ttu-id="28801-132">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="28801-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="28801-133">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="28801-133">The browser makes a request.</span></span>
1. <span data-ttu-id="28801-134">Varsayılan sayfa, genellikle *index.html*olan döndürülür.</span><span class="sxs-lookup"><span data-stu-id="28801-134">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="28801-135">Uygulamayı *index.html* önyükleme.</span><span class="sxs-lookup"><span data-stu-id="28801-135">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="28801-136">uygulamasının yönlendirici yüklenir ve Razor `Main` bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="28801-136">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="28801-137">Ana sayfada, `About` Blazor yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="28801-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="28801-138">\* Blazor Webassembly uygulamasındaki\* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="28801-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="28801-139">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="28801-139">The router handles the requests internally.</span></span>

<span data-ttu-id="28801-140">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="28801-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="28801-141">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="28801-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="28801-142">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri, fiziksel olarak sunucu üzerinde olmayan kaynakların tüm isteklerini *index.html* sayfasına yeniden yazması gerekir.</span><span class="sxs-lookup"><span data-stu-id="28801-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="28801-143">*index.html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="28801-143">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="28801-144">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayımlanan *web.config* dosyasıyla birlikte kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="28801-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="28801-145">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="28801-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="28801-146">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="28801-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="28801-147">*Barındırılan dağıtım* , Blazor webassembly uygulamasını Web sunucusunda çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılara sunar.</span><span class="sxs-lookup"><span data-stu-id="28801-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="28801-148">İstemci Blazor weelsembly uygulaması, sunucu uygulamasının */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="28801-148">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="28801-149">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="28801-149">The two apps are deployed together.</span></span> <span data-ttu-id="28801-150">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="28801-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="28801-151">Barındırılan bir dağıtım için Visual Studio, \*\* Blazor \*\* `blazorwasm` **barındırılan** seçenek seçili olarak ( [DotNet yeni](/dotnet/core/tools/dotnet-new) komut kullanılırken şablon), webassembly uygulama projesi şablonunu (komut kullanılırken) içerir `-ho|--hosted` `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="28801-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="28801-152">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="28801-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="28801-153">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="28801-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="28801-154">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="28801-154">Standalone deployment</span></span>

<span data-ttu-id="28801-155">*Tek başına dağıtım* , Blazor webassembly uygulamasına doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="28801-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="28801-156">Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.</span><span class="sxs-lookup"><span data-stu-id="28801-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="28801-157">Tek başına dağıtım varlıkları */BIN/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="28801-157">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="28801-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="28801-158">Azure App Service</span></span>

Blazor<span data-ttu-id="28801-159">WebAssembly uygulamaları, uygulamayı [IIS](#iis)üzerinde barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="28801-159"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="28801-160">BlazorLinux için Azure App Service tek başına webassembly uygulaması dağıtmak Şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="28801-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="28801-161">Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="28801-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="28801-162">Bu senaryoyu etkinleştirmek için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="28801-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="28801-163">IIS</span><span class="sxs-lookup"><span data-stu-id="28801-163">IIS</span></span>

<span data-ttu-id="28801-164">IIS, uygulamalar için özellikli bir statik dosya sunucusudur Blazor .</span><span class="sxs-lookup"><span data-stu-id="28801-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="28801-165">IIS 'yi barındıracak şekilde yapılandırmak için Blazor bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="28801-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="28801-166">Yayımlanan varlıklar */BIN/Release/{Target Framework}/Publish* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="28801-166">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="28801-167">Web sunucusunda veya barındırma hizmetinde *Yayımlama* klasörünün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="28801-167">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="28801-168">web.config</span><span class="sxs-lookup"><span data-stu-id="28801-168">web.config</span></span>

<span data-ttu-id="28801-169">Bir Blazor Proje yayımlandığında, AŞAĞıDAKI IIS yapılandırmasıyla bir *web.config* dosyası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="28801-169">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="28801-170">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="28801-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="28801-171">*. dll*:`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="28801-171">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="28801-172">*. JSON*:`application/json`</span><span class="sxs-lookup"><span data-stu-id="28801-172">*.json*: `application/json`</span></span>
  * <span data-ttu-id="28801-173">*...*:`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="28801-173">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="28801-174">*. WOFF*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="28801-174">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="28801-175">*. woff2*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="28801-175">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="28801-176">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="28801-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="28801-177">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="28801-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="28801-178">Uygulamanın statik varlıklarının bulunduğu alt dizini (*Wwwroot/{Path istenen}*) sunar.</span><span class="sxs-lookup"><span data-stu-id="28801-178">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="28801-179">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe (*Wwwroot/index.html*) uygulamanın varsayılan belgesine yeniden YÖNLENDIRILMESI için Spa geri dönüş yönlendirmesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="28801-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="28801-180">Özel bir web.config kullanma</span><span class="sxs-lookup"><span data-stu-id="28801-180">Use a custom web.config</span></span>

<span data-ttu-id="28801-181">Özel bir *web.config* dosyası kullanmak için, özel *web.config* dosyasını proje klasörünün köküne yerleştirin ve projeyi yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="28801-181">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="28801-182">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="28801-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="28801-183">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="28801-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="28801-184">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="28801-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="28801-185">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="28801-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="28801-186">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="28801-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="28801-187">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="28801-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="28801-188">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="28801-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="28801-189">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="28801-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="28801-190">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="28801-190">Copy the installer to the server.</span></span> <span data-ttu-id="28801-191">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="28801-191">Run the installer.</span></span> <span data-ttu-id="28801-192">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="28801-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="28801-193">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="28801-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="28801-194">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28801-194">Configure the website</span></span>

<span data-ttu-id="28801-195">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28801-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="28801-196">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="28801-196">The folder contains:</span></span>

* <span data-ttu-id="28801-197">Gerekli yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere, IIS 'nin Web sitesini yapılandırmak için kullandığı *web.config* dosyası.</span><span class="sxs-lookup"><span data-stu-id="28801-197">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="28801-198">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="28801-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="28801-199">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="28801-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="28801-200">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="28801-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="28801-201">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="28801-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="28801-202">BlazorDosyaya bir bölüm ekleyerek uygulamanın yayımlanmış *web.config* dosyasındaki işleyiciyi kaldırın `<handlers>` :</span><span class="sxs-lookup"><span data-stu-id="28801-202">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="28801-203">Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :</span><span class="sxs-lookup"><span data-stu-id="28801-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="28801-204">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:blazor/host-and-deploy/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="28801-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="28801-205">Uygulamanın *index.html* dosyasındaki uygulama temel yolunu, IIS 'de alt uygulamayı YAPıLANDıRıRKEN kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28801-205">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="28801-206">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="28801-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="28801-207">IIS, Brotli veya gzip sıkıştırılan varlıkları sunacak şekilde *web.config* aracılığıyla yapılandırılabilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="28801-207">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="28801-208">Örnek bir yapılandırma için bkz. [web.config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="28801-208">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="28801-209">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="28801-209">Troubleshooting</span></span>

<span data-ttu-id="28801-210">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="28801-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="28801-211">Modül yüklü olmadığında *web.config* dosyası IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="28801-211">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="28801-212">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller Blazor .</span><span class="sxs-lookup"><span data-stu-id="28801-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="28801-213">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="28801-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="28801-214">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="28801-214">Azure Storage</span></span>

<span data-ttu-id="28801-215">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="28801-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="28801-216">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="28801-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="28801-217">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="28801-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="28801-218">**Dizin belgesi adını** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="28801-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="28801-219">**Hata belge yolunu** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="28801-219">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="28801-220">bileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="28801-220"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="28801-221">Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında Blazor , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna**yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="28801-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="28801-222">*index.html* blobu döndürülür ve Blazor yönlendirici yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="28801-222">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="28801-223">Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="28801-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="28801-224">Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="28801-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="28801-225">`Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.</span><span class="sxs-lookup"><span data-stu-id="28801-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="28801-226">Her dosya için Depolama Gezgini (Azure portal):</span><span class="sxs-lookup"><span data-stu-id="28801-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="28801-227">Dosyaya sağ tıklayın ve **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="28801-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="28801-228">**ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="28801-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="28801-229">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="28801-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="28801-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="28801-230">Nginx</span></span>

<span data-ttu-id="28801-231">Aşağıdaki *NGINX. conf* dosyası, NGINX 'in diskteki karşılık gelen bir dosyayı bulamadığı her seferinde *index.html* dosyasını göndermek üzere nasıl yapılandırılacağını göstermek üzere basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="28801-231">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="28801-232">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="28801-232">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="28801-233">Docker 'da NGINX</span><span class="sxs-lookup"><span data-stu-id="28801-233">Nginx in Docker</span></span>

<span data-ttu-id="28801-234">BlazorNGINX kullanarak Docker 'da barındırmak Için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="28801-234">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="28801-235">*nginx.config* dosyasını kapsayıcıya kopyalamak Için Dockerfile 'ı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="28801-235">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="28801-236">Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="28801-236">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="28801-237">Apache</span><span class="sxs-lookup"><span data-stu-id="28801-237">Apache</span></span>

<span data-ttu-id="28801-238">BlazorCenelsembly uygulamasını CentOS 7 veya sonraki bir sürüme dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="28801-238">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="28801-239">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="28801-239">Create the Apache configuration file.</span></span> <span data-ttu-id="28801-240">Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="28801-240">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="28801-241">Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="28801-241">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="28801-242">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).</span><span class="sxs-lookup"><span data-stu-id="28801-242">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="28801-243">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="28801-243">Restart the Apache service.</span></span>

<span data-ttu-id="28801-244">Daha fazla bilgi için bkz. [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="28801-244">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="28801-245">GitHub sayfaları</span><span class="sxs-lookup"><span data-stu-id="28801-245">GitHub Pages</span></span>

<span data-ttu-id="28801-246">URL yeniden işlemesini işlemek için, isteği *index.html* sayfasına yönlendirmeyi işleyen bir komut dosyası ile *404.html* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="28801-246">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="28801-247">Topluluk tarafından sunulan örnek bir uygulama için bkz. [GitHub sayfaları Için tek sayfalı uygulamalar](https://spa-github-pages.rafrex.com/) ([GitHub üzerinde rafrex/Spa-GitHub-Pages](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="28801-247">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="28801-248">Topluluk yaklaşımını kullanan bir örnek, GitHub ([canlı site](https://blazor-demo.github.io/)) [üzerinde blazor-demo/blazor-demo. GitHub. IO](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir.</span><span class="sxs-lookup"><span data-stu-id="28801-248">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="28801-249">Bir kuruluş sitesi yerine bir proje sitesi kullanırken, `<base>` *index.html*'de etiketi ekleyin veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="28801-249">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="28801-250">`href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin,) ayarlayın `my-repository/` .</span><span class="sxs-lookup"><span data-stu-id="28801-250">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="28801-251">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="28801-251">Host configuration values</span></span>

<span data-ttu-id="28801-252">[ Blazor Webassembly uygulamaları](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="28801-252">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="28801-253">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="28801-253">Content root</span></span>

<span data-ttu-id="28801-254">`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="28801-254">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="28801-255">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="28801-255">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="28801-256">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="28801-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="28801-257">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="28801-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="28801-258">**IIS Express** profilindeki dosyanın *launchSettings.js* uygulamanın bir girdisini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="28801-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="28801-259">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="28801-259">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="28801-260">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="28801-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="28801-261">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini dosyasına *launchSettings.js* ekler.</span><span class="sxs-lookup"><span data-stu-id="28801-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="28801-262">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="28801-262">Path base</span></span>

<span data-ttu-id="28801-263">`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="28801-263">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="28801-264">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="28801-264">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="28801-265">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="28801-265">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="28801-266">Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` .</span><span class="sxs-lookup"><span data-stu-id="28801-266">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="28801-267">Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="28801-267">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="28801-268">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="28801-268">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="28801-269">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="28801-269">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="28801-270">**IIS Express** profilindeki dosyanın *launchSettings.js* uygulamanın bir girdisini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="28801-270">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="28801-271">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="28801-271">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="28801-272">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="28801-272">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="28801-273">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini dosyasına *launchSettings.js* ekler.</span><span class="sxs-lookup"><span data-stu-id="28801-273">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="28801-274">URL’ler</span><span class="sxs-lookup"><span data-stu-id="28801-274">URLs</span></span>

<span data-ttu-id="28801-275">`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="28801-275">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="28801-276">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="28801-276">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="28801-277">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="28801-277">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="28801-278">**IIS Express** profilindeki dosyanın *launchSettings.js* uygulamanın bir girdisini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="28801-278">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="28801-279">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="28801-279">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="28801-280">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="28801-280">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="28801-281">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini dosyasına *launchSettings.js* ekler.</span><span class="sxs-lookup"><span data-stu-id="28801-281">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="28801-282">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="28801-282">Configure the Linker</span></span>

Blazor<span data-ttu-id="28801-283">çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="28801-283"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="28801-284">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="28801-284">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="28801-285">Özel önyükleme kaynağı yükleme</span><span class="sxs-lookup"><span data-stu-id="28801-285">Custom boot resource loading</span></span>

<span data-ttu-id="28801-286">Blazor `loadBootResource` Yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için bir webassembly uygulaması işleviyle başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="28801-286">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="28801-287">`loadBootResource`Aşağıdaki senaryolar için kullanın:</span><span class="sxs-lookup"><span data-stu-id="28801-287">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="28801-288">Kullanıcıların bir CDN 'den saat dilimi verileri veya *DotNet. IStream* gibi statik kaynakları yüklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="28801-288">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="28801-289">Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="28801-289">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="28801-290">Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .</span><span class="sxs-lookup"><span data-stu-id="28801-290">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="28801-291">`loadBootResource`Parametreler aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="28801-291">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="28801-292">Parametre</span><span class="sxs-lookup"><span data-stu-id="28801-292">Parameter</span></span>    | <span data-ttu-id="28801-293">Açıklama</span><span class="sxs-lookup"><span data-stu-id="28801-293">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="28801-294">Kaynağın türü.</span><span class="sxs-lookup"><span data-stu-id="28801-294">The type of the resource.</span></span> <span data-ttu-id="28801-295">İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="28801-295">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="28801-296">Kaynağın adı.</span><span class="sxs-lookup"><span data-stu-id="28801-296">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="28801-297">Kaynağın göreli veya mutlak URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="28801-297">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="28801-298">Yanıtta beklenen içeriği temsil eden bütünlük dizesi.</span><span class="sxs-lookup"><span data-stu-id="28801-298">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="28801-299">`loadBootResource`yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:</span><span class="sxs-lookup"><span data-stu-id="28801-299">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="28801-300">URI dizesi.</span><span class="sxs-lookup"><span data-stu-id="28801-300">URI string.</span></span> <span data-ttu-id="28801-301">Aşağıdaki örnekte (*Wwwroot/index.html*), aşağıdaki dosyalar ŞURADA bir CDN 'den sunulur `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="28801-301">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="28801-302">*DotNet. \* . JS*</span><span class="sxs-lookup"><span data-stu-id="28801-302">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="28801-303">*DotNet.*</span><span class="sxs-lookup"><span data-stu-id="28801-303">*dotnet.wasm*</span></span>
  * <span data-ttu-id="28801-304">Saat dilimi verileri</span><span class="sxs-lookup"><span data-stu-id="28801-304">Timezone data</span></span>

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

* <span data-ttu-id="28801-305">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="28801-305">`Promise<Response>`.</span></span> <span data-ttu-id="28801-306">`integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.</span><span class="sxs-lookup"><span data-stu-id="28801-306">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="28801-307">Aşağıdaki örnek (*Wwwroot/index.html*) giden isteklere özel bir http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:</span><span class="sxs-lookup"><span data-stu-id="28801-307">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="28801-308">`null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.</span><span class="sxs-lookup"><span data-stu-id="28801-308">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="28801-309">Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="28801-309">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="28801-310">CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="28801-310">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="28801-311">Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="28801-311">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="28801-312">İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="28801-312">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="28801-313">DLL dosyalarının dosya adı uzantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="28801-313">Change the filename extension of DLL files</span></span>

<span data-ttu-id="28801-314">Uygulamanın yayımlanmış *. dll* dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa, bu bölümdeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="28801-314">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="28801-315">Uygulamayı yayımladıktan sonra, *. dll* dosyalarını farklı bir dosya uzantısı kullanacak şekilde yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın.</span><span class="sxs-lookup"><span data-stu-id="28801-315">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="28801-316">Uygulamanın yayımlanmış çıktısının *Wwwroot* dizinindeki *. dll* dosyalarını hedefleyin (ÖRNEĞIN, *{Content root}/bin/Release/netstandard2.1/Publish/Wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="28801-316">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="28801-317">Aşağıdaki örneklerde,. *DLL* dosyaları *. bin* dosya uzantısını kullanacak şekilde yeniden adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="28801-317">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="28801-318">Windows'da:</span><span class="sxs-lookup"><span data-stu-id="28801-318">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="28801-319">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="28801-319">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="28801-320">Linux veya macOS 'ta:</span><span class="sxs-lookup"><span data-stu-id="28801-320">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="28801-321">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="28801-321">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="28801-322">*. Bin*' den farklı bir dosya uzantısı kullanmak için, önceki komutlarda *. bin yerine.*</span><span class="sxs-lookup"><span data-stu-id="28801-322">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="28801-323">*. Gz* ve *blazor.boot.json.br* dosyalarında sıkıştırılanblazor.boot.jsgidermek için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="28801-323">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="28801-324">*. Gz* ve *blazor.boot.json.br* dosyalarında sıkıştırılanblazor.boot.jskaldırın.</span><span class="sxs-lookup"><span data-stu-id="28801-324">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="28801-325">Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="28801-325">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="28801-326">Dosyadaki güncelleştirilmiş *blazor.boot.js* yeniden sıkıştırın.</span><span class="sxs-lookup"><span data-stu-id="28801-326">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="28801-327">Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="28801-327">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="28801-328">*Wwwroot/service-worker-assets.js. br* ve *Wwwroot/service-worker-assets.js. gz*'yi kaldırın veya yeniden sıkıştırın.</span><span class="sxs-lookup"><span data-stu-id="28801-328">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="28801-329">Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="28801-329">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="28801-330">Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.</span><span class="sxs-lookup"><span data-stu-id="28801-330">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="28801-331">*ChangeDLLExtensions.ps1:*:</span><span class="sxs-lookup"><span data-stu-id="28801-331">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="28801-332">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="28801-332">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="28801-333">Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="28801-333">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="28801-334">Geri bildirim sağlamak için, [#5477 aspnetcore/sorunlar](https://github.com/dotnet/aspnetcore/issues/5477)' ı ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="28801-334">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
