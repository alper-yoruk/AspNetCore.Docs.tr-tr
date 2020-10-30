---
title: 'ASP.NET Core barındırma ve dağıtma :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: :::no-loc(Blazor):::ASP.NET Core, Içerik teslim ağları (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 0912b3fbcd0b891deb4985eaa18841c22f4f3264
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055756"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="8b7cb-103">ASP.NET Core barındırma ve dağıtma :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="8b7cb-103">Host and deploy ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="8b7cb-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından</span><span class="sxs-lookup"><span data-stu-id="8b7cb-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="8b7cb-105">[ :::no-loc(Blazor WebAssembly)::: Barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-105">With the [:::no-loc(Blazor WebAssembly)::: hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="8b7cb-106">:::no-loc(Blazor):::Uygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-106">The :::no-loc(Blazor)::: app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="8b7cb-107">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="8b7cb-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="8b7cb-109">:::no-loc(Blazor):::Uygulama, bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-109">The :::no-loc(Blazor)::: app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="8b7cb-110">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="8b7cb-111">:::no-loc(Blazor):::Uygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-111">The :::no-loc(Blazor)::: app is placed on a static hosting web server or service, where .NET isn't used to serve the :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="8b7cb-112">Bu strateji, bir uygulamayı IIS alt uygulaması olarak barındırma hakkında bilgi içeren [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a :::no-loc(Blazor WebAssembly)::: app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="8b7cb-113">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-113">Compression</span></span>

<span data-ttu-id="8b7cb-114">Bir :::no-loc(Blazor WebAssembly)::: uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-114">When a :::no-loc(Blazor WebAssembly)::: app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="8b7cb-115">Aşağıdaki sıkıştırma algoritmaları kullanılır:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="8b7cb-116">[Brotli](https://tools.ietf.org/html/rfc7932) (en yüksek düzey)</span><span class="sxs-lookup"><span data-stu-id="8b7cb-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="8b7cb-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="8b7cb-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="8b7cb-118">:::no-loc(Blazor)::: , uygun sıkıştırılmış dosyaları sunacak ana bilgisayarı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-118">:::no-loc(Blazor)::: relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="8b7cb-119">ASP.NET Core barındırılan bir proje kullanırken, konak proje içerik anlaşması yapabilir ve statik olarak sıkıştırılmış dosyalara hizmet verebilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="8b7cb-120">:::no-loc(Blazor WebAssembly):::Tek başına bir uygulamayı barındırırken, statik olarak sıkıştırılmış dosyaların sunulmasını sağlamak için ek çalışma gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-120">When hosting a :::no-loc(Blazor WebAssembly)::: standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="8b7cb-121">IIS `web.config` sıkıştırma yapılandırması Için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="8b7cb-122">GitHub sayfaları gibi statik olarak sıkıştırılmış dosya içeriği anlaşmasını desteklemeyen statik barındırma çözümlerinde barındırırken, Brotli sıkıştırılan dosyaları getirmek ve kodunu çözmek üzere uygulamayı yapılandırmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="8b7cb-123">[Google/Brotli GitHub deposundan](https://github.com/google/brotli)JavaScript Brotli kod çözücüsünü edinin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="8b7cb-124">2020 Eylül itibariyle, kod çözücü dosyasının adı `decode.js` ve deponun [ `js` klasöründe](https://github.com/google/brotli/tree/master/js)bulunur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="8b7cb-125">`decode.js` `decode.min.js` [Google/brotli GitHub deposundaki](https://github.com/google/brotli)betiğin () küçültülmüş sürümünde bir gerileme bulunur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="8b7cb-126">Betiği kendinize göre küçültün veya sorun penceresi ne kadar [NPM paketini](https://www.npmjs.com/package/brotli) kullanın [. Brotlişifre çözme decode.min.js (google/brotli #844) çözümlenmez](https://github.com/google/brotli/issues/844) .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="8b7cb-127">Bu bölümdeki örnek kod, betiğin **küçültülmüş** sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="8b7cb-128">Kod çözücüyü kullanmak için uygulamayı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-128">Update the app to use the decoder.</span></span> <span data-ttu-id="8b7cb-129">İçindeki kapanış etiketinin içindeki biçimlendirmeyi `<body>` aşağıdaki gibi değiştirin `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      :::no-loc(Blazor):::.start({
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
 
<span data-ttu-id="8b7cb-130">Sıkıştırmayı devre dışı bırakmak için `:::no-loc(Blazor):::EnableCompression` uygulamanın proje dosyasına MSBuild özelliğini ekleyin ve değeri şu şekilde ayarlayın `false` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-130">To disable compression, add the `:::no-loc(Blazor):::EnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::EnableCompression>false</:::no-loc(Blazor):::EnableCompression>
</PropertyGroup>
```

<span data-ttu-id="8b7cb-131">`:::no-loc(Blazor):::EnableCompression`Özelliği [`dotnet publish`](/dotnet/core/tools/dotnet-publish) komut kabuğunda aşağıdaki söz dizimi ile komuta geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-131">The `:::no-loc(Blazor):::EnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p::::no-loc(Blazor):::EnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="8b7cb-132">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="8b7cb-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="8b7cb-133">Bir uygulamadaki sayfa bileşenlerine yönelik yönlendirme istekleri :::no-loc(Blazor WebAssembly)::: , barındırılan bir uygulamadaki yönlendirme istekleri kadar basittir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-133">Routing requests for page components in a :::no-loc(Blazor WebAssembly)::: app isn't as straightforward as routing requests in a :::no-loc(Blazor Server):::, hosted app.</span></span> <span data-ttu-id="8b7cb-134">:::no-loc(Blazor WebAssembly):::İki bileşeni olan bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-134">Consider a :::no-loc(Blazor WebAssembly)::: app with two components:</span></span>

* <span data-ttu-id="8b7cb-135">`Main.razor`: Uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="8b7cb-136">`About.razor`: `About` bileşen.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="8b7cb-137">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="8b7cb-138">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-138">The browser makes a request.</span></span>
1. <span data-ttu-id="8b7cb-139">Varsayılan sayfa döndürülür, genellikle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="8b7cb-140">`index.html` uygulamayı önyükleme.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="8b7cb-141">:::no-loc(Blazor):::uygulamasının yönlendirici yüklenir ve :::no-loc(Razor)::: `Main` bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-141">:::no-loc(Blazor):::'s router loads, and the :::no-loc(Razor)::: `Main` component is rendered.</span></span>

<span data-ttu-id="8b7cb-142">Ana sayfada, `About` :::no-loc(Blazor)::: yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-142">In the Main page, selecting the link to the `About` component works on the client because the :::no-loc(Blazor)::: router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="8b7cb-143">*:::no-loc(Blazor WebAssembly)::: Uygulamadaki* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-143">All of the requests for internal endpoints *within the :::no-loc(Blazor WebAssembly)::: app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="8b7cb-144">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-144">The router handles the requests internally.</span></span>

<span data-ttu-id="8b7cb-145">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="8b7cb-146">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="8b7cb-147">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri sunucuda fiziksel olarak olmayan tüm kaynak isteklerini sayfaya yeniden yazması gerekir `index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="8b7cb-148">`index.html`Döndürüldüğünde, uygulamanın :::no-loc(Blazor)::: yönlendiricisi doğru kaynakla sürer ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-148">When `index.html` is returned, the app's :::no-loc(Blazor)::: router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="8b7cb-149">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayımlanan dosyasıyla birlikte kullanabilirsiniz `web.config` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="8b7cb-150">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="8b7cb-151">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="8b7cb-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="8b7cb-152">*Barındırılan bir dağıtım* , :::no-loc(Blazor WebAssembly)::: uygulamayı bir Web sunucusu üzerinde çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılarına sunar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-152">A *hosted deployment* serves the :::no-loc(Blazor WebAssembly)::: app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="8b7cb-153">İstemci uygulaması, sunucu uygulamasının :::no-loc(Blazor WebAssembly)::: `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` diğer statik Web varlıklarıyla birlikte sunucu uygulamasının klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-153">The client :::no-loc(Blazor WebAssembly)::: app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="8b7cb-154">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-154">The two apps are deployed together.</span></span> <span data-ttu-id="8b7cb-155">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8b7cb-156">Barındırılan bir dağıtım için, Visual Studio **:::no-loc(Blazor WebAssembly)::: uygulama** proje şablonunu (komutunu kullanırken `blazorwasm` şablon [`dotnet new`](/dotnet/core/tools/dotnet-new) ), **`Hosted`** seçeneği belirlendiğinde ( `-ho|--hosted` `dotnet new` komutunu kullanırken) içerir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-156">For a hosted deployment, Visual Studio includes the **:::no-loc(Blazor WebAssembly)::: App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="8b7cb-157">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="8b7cb-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="8b7cb-158">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="8b7cb-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="8b7cb-159">Birden çok uygulama ile barındırılan dağıtım :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="8b7cb-159">Hosted deployment with multiple :::no-loc(Blazor WebAssembly)::: apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="8b7cb-160">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8b7cb-160">App configuration</span></span>

<span data-ttu-id="8b7cb-161">Barındırılan bir :::no-loc(Blazor)::: çözümü birden çok uygulamaya yönelik olarak yapılandırmak için :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-161">To configure a hosted :::no-loc(Blazor)::: solution to serve multiple :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* <span data-ttu-id="8b7cb-162">:::no-loc(Blazor):::Barındırılan proje şablonundan mevcut bir barındırılan çözümü kullanın veya yeni bir çözüm oluşturun :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-162">Use an existing hosted :::no-loc(Blazor)::: solution or create a new solution from the :::no-loc(Blazor)::: Hosted project template.</span></span>

* <span data-ttu-id="8b7cb-163">İstemci uygulamasının proje dosyasında, `<StaticWebAssetBasePath>` `<PropertyGroup>` `FirstApp` projenin statik varlıkların temel yolunu ayarlamak için değeri ile öğesine bir özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="8b7cb-164">Çözüme ikinci bir istemci uygulaması ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="8b7cb-165">Çözümün klasörüne adlı bir klasör ekleyin `SecondClient` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="8b7cb-166">:::no-loc(Blazor WebAssembly)::: `Second:::no-loc(Blazor):::App.Client` `SecondClient` Proje şablonundan klasöründe adlı bir uygulama oluşturun :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-166">Create a :::no-loc(Blazor WebAssembly)::: app named `Second:::no-loc(Blazor):::App.Client` in the `SecondClient` folder from the :::no-loc(Blazor WebAssembly)::: project template.</span></span>
  * <span data-ttu-id="8b7cb-167">Uygulamanın proje dosyasında:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-167">In the app's project file:</span></span>

    * <span data-ttu-id="8b7cb-168">`<StaticWebAssetBasePath>`Değerine sahip öğesine bir özelliği ekleyin `<PropertyGroup>` `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="8b7cb-169">Projeye bir proje başvurusu ekleyin `Shared` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="8b7cb-170">Yer tutucu `{SOLUTION NAME}` çözümün adıdır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="8b7cb-171">Sunucu uygulamasının proje dosyasında, eklenen istemci uygulaması için bir proje başvurusu oluşturun:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\Second:::no-loc(Blazor):::App.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="8b7cb-172">Sunucu uygulamasının `Properties/launchSettings.json` dosyasında, `applicationUrl` `{SOLUTION NAME}.Server` 5001 ve 5002 bağlantı noktalarında istemci uygulamalarına erişmek için Kestrel profili () öğesini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="8b7cb-173">Sunucu uygulamasının `Startup.Configure` yönteminde ( `Startup.cs` ), çağrısından sonra görünen aşağıdaki satırları kaldırın <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.Use:::no-loc(Blazor):::FrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.Map:::no-loc(Razor):::Pages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="8b7cb-174">İstekleri istemci uygulamalarına eşleyen ara yazılım ekleme.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="8b7cb-175">Aşağıdaki örnek, şu durumlarda ara yazılımı çalıştıracak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="8b7cb-176">İstek bağlantı noktası, özgün istemci uygulaması için 5001 ya da eklenen istemci uygulaması için 5002 ' dir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="8b7cb-177">İstek Konağı, `firstapp.com` özgün istemci uygulaması ya da `secondapp.com` eklenen istemci uygulaması için.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="8b7cb-178">Bu bölümde gösterilen örnekte, için ek yapılandırma gerekir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="8b7cb-179">Örnek ana bilgisayar etki alanlarındaki uygulamalara erişme `firstapp.com` ve `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="8b7cb-180">TLS güvenliğini etkinleştirmek için istemci uygulamalarına yönelik Sertifikalar (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="8b7cb-181">Gerekli yapılandırma Bu makalenin kapsamı dışındadır ve çözümün nasıl barındırıldığını bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="8b7cb-182">Daha fazla bilgi için bkz. [konak ve dağıtım makaleleri](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="8b7cb-183">Aşağıdaki kodu satırların daha önce kaldırıldığı yere yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.Use:::no-loc(Blazor):::FrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.Use:::no-loc(Blazor):::FrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="8b7cb-184">Sunucu uygulamasının Hava durumu tahmin denetleyicisi 'nde ( `Controllers/WeatherForecastController.cs` ), var olan yolu ( `[Route("[controller]")]` ) `WeatherForecastController` aşağıdaki yollarla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="8b7cb-185">Sunucu uygulamasının yöntemine eklenen ara yazılım, `Startup.Configure` gelen istekleri `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` bağlantı noktasına (5001/5002) veya etki alanına () bağlı olarak ya da öğesine göre değiştirir `firstapp.com` / `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="8b7cb-186">Sunucu uygulamasından istemci uygulamalarına Hava durumu verilerini döndürmek için önceki denetleyici yolları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="8b7cb-187">Statik varlıklar ve sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="8b7cb-187">Static assets and class libraries</span></span>

<span data-ttu-id="8b7cb-188">Statik varlıklar için aşağıdaki yaklaşımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="8b7cb-189">Varlık, istemci uygulamasının `wwwroot` klasöründe olduğunda, kendi yollarını normal olarak sağlayın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="8b7cb-190">Varlık `wwwroot` bir [ :::no-loc(Razor)::: sınıf kitaplığı (RCL)](xref:blazor/components/class-libraries)klasöründe olduğunda, [RCL makalesindeki](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)kılavuzluk uyarınca istemci uygulamasındaki statik varlığa başvurun:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-190">When the asset is in the `wwwroot` folder of a [:::no-loc(Razor)::: Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="8b7cb-191">Bir sınıf kitaplığı tarafından bir istemci uygulamasına verilen bileşenlere normal olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="8b7cb-192">Herhangi bir bileşen stil sayfaları veya JavaScript dosyaları gerektiriyorsa, istemci uygulamanın `wwwroot/index.html` dosyası doğru statik varlık bağlantılarını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-192">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="8b7cb-193">Bu yaklaşımlar aşağıdaki örneklerde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-193">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="8b7cb-194">Aşağıdaki `Jeep` bileşeni istemci uygulamalarından birine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-194">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="8b7cb-195">`Jeep`Bileşen şunları kullanır:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-195">The `Jeep` component uses:</span></span>

* <span data-ttu-id="8b7cb-196">İstemci uygulamanın `wwwroot` klasöründen () bir resim `jeep-cj.png` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-196">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="8b7cb-197">[Eklenen :::no-loc(Razor)::: Bileşen kitaplığı](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` klasöründen () bir resim `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-197">An image from an [added :::no-loc(Razor)::: component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="8b7cb-198">Örnek bileşen ( `Component1` ), kitaplık çözüme eklendiğinde RCL proje şablonu tarafından otomatik olarak oluşturulur `JeepImage` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-198">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="8b7cb-199">Görüntülerin sahibi olmadığınız takdirde, taşıtlar görüntülerini **herkese yayımlamayın** .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-199">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="8b7cb-200">Aksi takdirde, telif hakkı ihlali riski vardır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-200">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="8b7cb-201">Kitaplığın `jeep-yj.png` görüntüsü kitaplığın bileşenine de eklenebilir `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-201">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="8b7cb-202">İstemci uygulamasının dosyası, `wwwroot/index.html` aşağıdaki eklenmiş etikete sahip kitaplığın stil sayfasını ister `<link>` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-202">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="8b7cb-203">`Jeep`İstemci uygulamasının bileşeninde bileşene gezinti ekleyin `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-203">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="8b7cb-204">RCLs hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-204">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="8b7cb-205">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="8b7cb-205">Standalone deployment</span></span>

<span data-ttu-id="8b7cb-206">*Tek başına dağıtım* , :::no-loc(Blazor WebAssembly)::: uygulamaya doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-206">A *standalone deployment* serves the :::no-loc(Blazor WebAssembly)::: app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="8b7cb-207">Herhangi bir statik dosya sunucusu, :::no-loc(Blazor)::: uygulamayı sunabilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-207">Any static file server is able to serve the :::no-loc(Blazor)::: app.</span></span>

<span data-ttu-id="8b7cb-208">Tek başına dağıtım varlıkları `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-208">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="8b7cb-209">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8b7cb-209">Azure App Service</span></span>

<span data-ttu-id="8b7cb-210">:::no-loc(Blazor WebAssembly)::: uygulamalar, uygulamayı [IIS](#iis)'de barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-210">:::no-loc(Blazor WebAssembly)::: apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="8b7cb-211">Tek başına bir :::no-loc(Blazor WebAssembly)::: uygulamanın Linux için Azure App Service dağıtımı şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-211">Deploying a standalone :::no-loc(Blazor WebAssembly)::: app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="8b7cb-212">Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-212">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="8b7cb-213">Bu senaryoyu etkinleştirmek için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-213">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="8b7cb-214">IIS</span><span class="sxs-lookup"><span data-stu-id="8b7cb-214">IIS</span></span>

<span data-ttu-id="8b7cb-215">IIS, uygulamalar için özellikli bir statik dosya sunucusudur :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-215">IIS is a capable static file server for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="8b7cb-216">IIS 'yi barındıracak şekilde yapılandırmak için :::no-loc(Blazor)::: bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-216">To configure IIS to host :::no-loc(Blazor):::, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="8b7cb-217">Yayımlanan varlıklar `/bin/Release/{TARGET FRAMEWORK}/publish` klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-217">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="8b7cb-218">`publish`Web sunucusunda veya barındırma hizmetinde klasörün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-218">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="8b7cb-219">web.config</span><span class="sxs-lookup"><span data-stu-id="8b7cb-219">web.config</span></span>

<span data-ttu-id="8b7cb-220">Bir :::no-loc(Blazor)::: Proje yayımlandığında, `web.config` aşağıdaki IIS yapılandırmasıyla bir dosya oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-220">When a :::no-loc(Blazor)::: project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="8b7cb-221">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-221">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="8b7cb-222">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="8b7cb-222">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="8b7cb-223">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="8b7cb-223">`.json`: `application/json`</span></span>
  * <span data-ttu-id="8b7cb-224">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="8b7cb-224">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="8b7cb-225">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="8b7cb-225">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="8b7cb-226">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="8b7cb-226">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="8b7cb-227">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-227">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="8b7cb-228">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-228">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="8b7cb-229">Uygulamanın statik varlıklarının bulunduğu alt dizini ( `wwwroot/{PATH REQUESTED}` ) sunar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-229">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="8b7cb-230">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe () uygulamanın varsayılan belgesine yeniden yönlendirilmesi için SPA geri dönüş yönlendirmesi oluşturun `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-230">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="8b7cb-231">Özel bir web.config kullanma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-231">Use a custom web.config</span></span>

<span data-ttu-id="8b7cb-232">Özel bir dosya kullanmak için `web.config` , özel `web.config` dosyayı proje klasörünün köküne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-232">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="8b7cb-233">Projeyi uygulamanın proje dosyasında kullanarak IIS 'e özgü varlıkları yayımlayacak şekilde yapılandırın `PublishIISAssets` ve projeyi yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-233">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="8b7cb-234">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="8b7cb-234">Install the URL Rewrite Module</span></span>

<span data-ttu-id="8b7cb-235">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-235">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="8b7cb-236">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-236">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="8b7cb-237">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-237">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="8b7cb-238">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-238">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="8b7cb-239">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-239">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="8b7cb-240">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-240">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="8b7cb-241">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-241">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="8b7cb-242">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-242">Copy the installer to the server.</span></span> <span data-ttu-id="8b7cb-243">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-243">Run the installer.</span></span> <span data-ttu-id="8b7cb-244">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-244">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="8b7cb-245">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-245">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="8b7cb-246">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-246">Configure the website</span></span>

<span data-ttu-id="8b7cb-247">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-247">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="8b7cb-248">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-248">The folder contains:</span></span>

* <span data-ttu-id="8b7cb-249">`web.config`Web sitesini yapılandırmak için gereken yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS tarafından kullanılan dosya.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-249">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="8b7cb-250">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-250">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="8b7cb-251">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-251">Host as an IIS sub-app</span></span>

<span data-ttu-id="8b7cb-252">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-252">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="8b7cb-253">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-253">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="8b7cb-254">:::no-loc(Blazor)::: `web.config` Dosyaya bir bölüm ekleyerek uygulamanın yayımlanmış dosyasındaki işleyiciyi kaldırın `<handlers>` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-254">Remove the handler in the :::no-loc(Blazor)::: app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="8b7cb-255">Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-255">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="8b7cb-256">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:blazor/host-and-deploy/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-256">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="8b7cb-257">Uygulamanın dosyasındaki uygulama temel yolunu, `index.html` IIS 'de alt uygulamayı yapılandırırken kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-257">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="8b7cb-258">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="8b7cb-258">Brotli and Gzip compression</span></span>

<span data-ttu-id="8b7cb-259">IIS, `web.config` Brotli veya gzip ile sıkıştırılan varlıkları sunacak şekilde yapılandırılabilir :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-259">IIS can be configured via `web.config` to serve Brotli or Gzip compressed :::no-loc(Blazor)::: assets.</span></span> <span data-ttu-id="8b7cb-260">Örnek bir yapılandırma için bkz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) ..</span><span class="sxs-lookup"><span data-stu-id="8b7cb-260">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="8b7cb-261">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="8b7cb-261">Troubleshooting</span></span>

<span data-ttu-id="8b7cb-262">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-262">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="8b7cb-263">Modül yüklü olmadığında, `web.config` Dosya IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-263">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="8b7cb-264">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-264">This prevents the IIS Manager from loading the website's configuration and the website from serving :::no-loc(Blazor):::'s static files.</span></span>

<span data-ttu-id="8b7cb-265">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="8b7cb-265">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="8b7cb-266">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="8b7cb-266">Azure Storage</span></span>

<span data-ttu-id="8b7cb-267">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz :::no-loc(Blazor)::: uygulama barındırmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-267">[Azure Storage](/azure/storage/) static file hosting allows serverless :::no-loc(Blazor)::: app hosting.</span></span> <span data-ttu-id="8b7cb-268">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-268">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="8b7cb-269">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-269">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="8b7cb-270">**Dizin belgesi adını** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-270">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="8b7cb-271">**Hata belge yolunu** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-271">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="8b7cb-272">:::no-loc(Razor)::: bileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-272">:::no-loc(Razor)::: components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="8b7cb-273">Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında :::no-loc(Blazor)::: , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna** yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-273">When a request for one of these resources is received that the :::no-loc(Blazor)::: router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path** .</span></span> <span data-ttu-id="8b7cb-274">`index.html`BLOB döndürülür ve :::no-loc(Blazor)::: yönlendirici yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-274">The `index.html` blob is returned, and the :::no-loc(Blazor)::: router loads and processes the path.</span></span>

<span data-ttu-id="8b7cb-275">Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-275">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="8b7cb-276">Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-276">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="8b7cb-277">`Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-277">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="8b7cb-278">Her dosya için Depolama Gezgini (Azure portal):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-278">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="8b7cb-279">Dosyaya sağ tıklayın ve **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-279">Right-click the file and select **Properties** .</span></span>
  1. <span data-ttu-id="8b7cb-280">**ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-280">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="8b7cb-281">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-281">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="8b7cb-282">Nginx</span><span class="sxs-lookup"><span data-stu-id="8b7cb-282">Nginx</span></span>

<span data-ttu-id="8b7cb-283">Aşağıdaki `nginx.conf` Dosya, NGINX 'in `index.html` diskteki karşılık gelen bir dosyayı bulamadığı her seferinde dosyayı göndermek üzere nasıl yapılandırılacağını göstermek için basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-283">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="8b7cb-284">[NGINX veri bloğu hızı sınırı](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) ile ayarlandığında [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , :::no-loc(Blazor WebAssembly)::: uygulamalar `burst` bir uygulama tarafından yapılan görece çok sayıda isteği karşılamak için büyük bir parametre değeri gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-284">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), :::no-loc(Blazor WebAssembly)::: apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="8b7cb-285">Başlangıçta değeri en az 60 olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-285">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="8b7cb-286">Tarayıcı geliştirici araçları veya ağ trafiği Aracı, isteklerin *503 servis dışı* bir durum kodu aldığını gösteriyorsa değeri artırın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-286">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="8b7cb-287">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-287">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="8b7cb-288">Docker 'da NGINX</span><span class="sxs-lookup"><span data-stu-id="8b7cb-288">Nginx in Docker</span></span>

<span data-ttu-id="8b7cb-289">:::no-loc(Blazor):::NGINX kullanarak Docker 'da barındırmak Için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-289">To host :::no-loc(Blazor)::: in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="8b7cb-290">Dockerfile dosyasını, dosyayı kapsayıcıya kopyalamak için güncelleştirin `nginx.config` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-290">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="8b7cb-291">Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-291">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="8b7cb-292">Apache</span><span class="sxs-lookup"><span data-stu-id="8b7cb-292">Apache</span></span>

<span data-ttu-id="8b7cb-293">Bir :::no-loc(Blazor WebAssembly)::: uygulamayı CentOS 7 veya sonraki bir sürüme dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-293">To deploy a :::no-loc(Blazor WebAssembly)::: app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="8b7cb-294">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-294">Create the Apache configuration file.</span></span> <span data-ttu-id="8b7cb-295">Aşağıdaki örnek, Basitleştirilmiş bir yapılandırma dosyasıdır ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-295">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="8b7cb-296">Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-296">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="8b7cb-297">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-297">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="8b7cb-298">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-298">Restart the Apache service.</span></span>

<span data-ttu-id="8b7cb-299">Daha fazla bilgi için bkz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) . ve.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-299">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="8b7cb-300">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="8b7cb-300">GitHub Pages</span></span>

<span data-ttu-id="8b7cb-301">URL yeniden işlemesini işlemek için, `wwwroot/404.html` isteği sayfaya yönlendirmeyi işleyen bir betiği olan bir dosya ekleyin `index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-301">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="8b7cb-302">Bir örnek için bkz. [Stevesandersonms/ :::no-loc(Blazor)::: ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="8b7cb-302">For an example, see the [SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="8b7cb-303">[Canlı site](https://stevesandersonms.github.io/:::no-loc(Blazor):::OnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="8b7cb-303">[Live site](https://stevesandersonms.github.io/:::no-loc(Blazor):::OnGitHubPages/))</span></span>

<span data-ttu-id="8b7cb-304">Bir kuruluş sitesi yerine bir proje sitesi kullanırken, `<base>` içindeki etiketini güncelleştirin `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-304">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="8b7cb-305">`href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin, `/my-repository/` ) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-305">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="8b7cb-306">[Stevesandersonms/ :::no-loc(Blazor)::: ongithubpages GitHub deposunda](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages), temel `href` [ `.github/workflows/main.yml` yapılandırma dosyası](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages/blob/master/.github/workflows/main.yml)tarafından yayımlama sırasında güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-306">In the [SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="8b7cb-307">[Stevesandersonms/ :::no-loc(Blazor)::: ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages) , .net Foundation veya Microsoft tarafından sahip değil, korunmuyor veya desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-307">The [SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8b7cb-308">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="8b7cb-308">Host configuration values</span></span>

<span data-ttu-id="8b7cb-309">[ :::no-loc(Blazor WebAssembly)::: uygulamalar](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında komut satırı bağımsız değişkenleri olarak aşağıdaki ana bilgisayar yapılandırma değerlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-309">[:::no-loc(Blazor WebAssembly)::: apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="8b7cb-310">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="8b7cb-310">Content root</span></span>

<span data-ttu-id="8b7cb-311">`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-311">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="8b7cb-312">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-312">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="8b7cb-313">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-313">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8b7cb-314">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-314">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="8b7cb-315">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8b7cb-315">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="8b7cb-316">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-316">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="8b7cb-317">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-317">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments** .</span></span> <span data-ttu-id="8b7cb-318">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-318">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="8b7cb-319">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="8b7cb-319">Path base</span></span>

<span data-ttu-id="8b7cb-320">`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-320">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="8b7cb-321">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-321">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="8b7cb-322">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-322">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8b7cb-323">Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-323">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="8b7cb-324">Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-324">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="8b7cb-325">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-325">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8b7cb-326">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-326">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="8b7cb-327">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8b7cb-327">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="8b7cb-328">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-328">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="8b7cb-329">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-329">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments** .</span></span> <span data-ttu-id="8b7cb-330">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-330">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="8b7cb-331">URL’ler</span><span class="sxs-lookup"><span data-stu-id="8b7cb-331">URLs</span></span>

<span data-ttu-id="8b7cb-332">`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-332">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="8b7cb-333">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8b7cb-334">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="8b7cb-335">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8b7cb-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="8b7cb-336">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="8b7cb-337">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments** .</span></span> <span data-ttu-id="8b7cb-338">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="8b7cb-339">Kesiciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-339">Configure the Trimmer</span></span>

<span data-ttu-id="8b7cb-340">:::no-loc(Blazor)::: çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) kırpılmasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-340">:::no-loc(Blazor)::: performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="8b7cb-341">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-341">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="8b7cb-342">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-342">Configure the Linker</span></span>

<span data-ttu-id="8b7cb-343">:::no-loc(Blazor)::: çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-343">:::no-loc(Blazor)::: performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="8b7cb-344">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-344">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="8b7cb-345">Özel önyükleme kaynağı yükleme</span><span class="sxs-lookup"><span data-stu-id="8b7cb-345">Custom boot resource loading</span></span>

<span data-ttu-id="8b7cb-346">:::no-loc(Blazor WebAssembly):::Uygulama, `loadBootResource` yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için işleviyle başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-346">A :::no-loc(Blazor WebAssembly)::: app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="8b7cb-347">`loadBootResource`Aşağıdaki senaryolar için kullanın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-347">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="8b7cb-348">Kullanıcıların saat dilimi verileri veya bir CDN 'den statik kaynakları yüklemesine izin verin `dotnet.wasm` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-348">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="8b7cb-349">Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-349">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="8b7cb-350">Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-350">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="8b7cb-351">`loadBootResource` Parametreler aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-351">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="8b7cb-352">Parametre</span><span class="sxs-lookup"><span data-stu-id="8b7cb-352">Parameter</span></span>    | <span data-ttu-id="8b7cb-353">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8b7cb-353">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="8b7cb-354">Kaynağın türü.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-354">The type of the resource.</span></span> <span data-ttu-id="8b7cb-355">İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="8b7cb-355">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="8b7cb-356">Kaynağın adı.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-356">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="8b7cb-357">Kaynağın göreli veya mutlak URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-357">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="8b7cb-358">Yanıtta beklenen içeriği temsil eden bütünlük dizesi.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-358">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="8b7cb-359">`loadBootResource` yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-359">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="8b7cb-360">URI dizesi.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-360">URI string.</span></span> <span data-ttu-id="8b7cb-361">Aşağıdaki örnekte ( `wwwroot/index.html` ), aşağıdaki dosyalar ' de BIR CDN 'den sunulur `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-361">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="8b7cb-362">Saat dilimi verileri</span><span class="sxs-lookup"><span data-stu-id="8b7cb-362">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    :::no-loc(Blazor):::.start({
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

* <span data-ttu-id="8b7cb-363">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-363">`Promise<Response>`.</span></span> <span data-ttu-id="8b7cb-364">`integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-364">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="8b7cb-365">Aşağıdaki örnek ( `wwwroot/index.html` ) giden isteklere özel bır http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-365">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    :::no-loc(Blazor):::.start({
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

* <span data-ttu-id="8b7cb-366">`null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-366">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="8b7cb-367">Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-367">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="8b7cb-368">CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-368">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="8b7cb-369">Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-369">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="8b7cb-370">İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-370">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="8b7cb-371">DLL dosyalarının dosya adı uzantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="8b7cb-371">Change the filename extension of DLL files</span></span>

<span data-ttu-id="8b7cb-372">Uygulamanın yayımlanmış dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa `.dll` , bu bölümdeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-372">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="8b7cb-373">Uygulamayı yayımladıktan sonra, `.dll` farklı bir dosya uzantısı kullanmak üzere dosyaları yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-373">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="8b7cb-374">`.dll` `wwwroot` Uygulamanın yayınlanan çıktısının dizinindeki dosyaları hedefleyin (örneğin, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-374">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="8b7cb-375">Aşağıdaki örneklerde `.dll` Dosyalar dosya uzantısını kullanacak şekilde yeniden adlandırılır `.bin` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-375">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="8b7cb-376">Windows'da:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-376">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="8b7cb-377">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-377">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="8b7cb-378">Linux veya macOS 'ta:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-378">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="8b7cb-379">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-379">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="8b7cb-380">Farklı bir dosya uzantısını kullanmak için `.bin` `.bin` Yukarıdaki komutlarda değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-380">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="8b7cb-381">Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları çözmek için `blazor.boot.json.br` aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-381">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="8b7cb-382">Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları kaldırın `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-382">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="8b7cb-383">Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-383">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="8b7cb-384">Güncelleştirilmiş dosyayı yeniden sıkıştırın `blazor.boot.json` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-384">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="8b7cb-385">Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-385">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="8b7cb-386">Ve ' i kaldırın veya yeniden sıkıştırın `wwwroot/service-worker-assets.js.br` `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-386">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="8b7cb-387">Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-387">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="8b7cb-388">Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-388">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="8b7cb-389">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-389">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="8b7cb-390">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-390">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="8b7cb-391">Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-391">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="8b7cb-392">Aynı derlemeleri yeniden adlandırırken ve geç yüklerken, içindeki kılavuza bakın <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-392">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="8b7cb-393">Bütünlük denetimi başarısızlıklarını çözümleyin</span><span class="sxs-lookup"><span data-stu-id="8b7cb-393">Resolve integrity check failures</span></span>

<span data-ttu-id="8b7cb-394">:::no-loc(Blazor WebAssembly):::Uygulamanın başlangıç dosyalarını indirdiğinde, tarayıcıya yanıtlar üzerinde bütünlük denetimleri gerçekleştirmesini söyler.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-394">When :::no-loc(Blazor WebAssembly)::: downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="8b7cb-395">`blazor.boot.json` `.dll` , Ve diğer dosyalar IÇIN beklenen SHA-256 karma değerlerini belirtmek için dosyadaki bilgileri kullanır `.wasm` .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-395">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="8b7cb-396">Bu, aşağıdaki nedenlerle faydalıdır:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-396">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="8b7cb-397">Örneğin, Kullanıcı uygulama dosyalarını indirme sürecinde olduğunda, Web sunucunuza yeni bir dağıtım uygulanmışsa, tutarsız bir dosya kümesini yükleme riskini almanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-397">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="8b7cb-398">Tutarsız dosyalar tanımsız davranışa yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-398">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="8b7cb-399">Kullanıcının tarayıcısının hiçbir şekilde tutarsız veya geçersiz yanıtları önbelleklemesini sağlar, bu da sayfayı el ile yenilese bile uygulamayı başlatmalarını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-399">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="8b7cb-400">Yanıtları önbelleğe alma işlemini güvenli hale getirir ve beklenen SHA-256 karmaları değişene kadar sunucu tarafı değişikliklerini kontrol etmez, sonraki sayfa yüklemeleri daha az istek içerir ve çok daha hızlı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-400">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="8b7cb-401">Web sunucunuz beklenen SHA-256 karmalarıyla eşleşmeyen yanıtlar döndürürse, tarayıcının geliştirici konsolunda aşağıdakine benzer bir hata görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-401">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/My:::no-loc(Blazor):::App.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="8b7cb-402">Çoğu durumda bu, bütünlük denetimi ile ilgili bir sorun *değildir* .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-402">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="8b7cb-403">Bunun yerine, başka bir sorun olduğu anlamına gelir ve bu sorunu gidermek için bütünlük denetimi size uyarı verebilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-403">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="8b7cb-404">Bütünlük sorunlarını tanılama</span><span class="sxs-lookup"><span data-stu-id="8b7cb-404">Diagnosing integrity problems</span></span>

<span data-ttu-id="8b7cb-405">Bir uygulama oluşturulduğunda, oluşturulan `blazor.boot.json` bildirim, derleme çıktısının oluşturulduğu sırada önyükleme KAYNAKLARıNıZıN SHA-256 karmalarını (örneğin,, `.dll` `.wasm` ve diğer dosyaları) açıklar.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-405">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="8b7cb-406">' Deki SHA-256 karmaları `blazor.boot.json` tarayıcıya teslim edilen dosyalarla eşleştiği sürece bütünlük denetimi geçirilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-406">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="8b7cb-407">Bunun başarısız olmasının yaygın nedenleri:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-407">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="8b7cb-408">Web sunucusunun yanıtı, tarayıcı istenen dosya yerine bir hatadır (örneğin, bir *404-bulunamadı* veya *500-Internal Server Error* ).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-408">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error* ) instead of the file the browser requested.</span></span> <span data-ttu-id="8b7cb-409">Bu, tarayıcı tarafından bir bütünlük denetimi hatası olarak bildirilir ve yanıt hatası olarak değildir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-409">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="8b7cb-410">Dosya içeriğini tarayıcıya, dosyaların oluşturulması ve teslimi arasında değiştiren bir sorun.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-410">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="8b7cb-411">Bu durum oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-411">This might happen:</span></span>
   * <span data-ttu-id="8b7cb-412">Ya da yapı araçları derleme çıkışını el ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-412">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="8b7cb-413">Dağıtım işleminin bazı bir yönü dosyaları değiştirdiyseniz.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-413">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="8b7cb-414">Örneğin, git tabanlı bir dağıtım mekanizması kullanıyorsanız, Windows üzerinde dosya oluşturup Linux 'ta kullanıma alırsanız git 'in Windows stili satır sonlarını şeffaf olarak UNIX stili satır sonlarına dönüştürdüğünü göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-414">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="8b7cb-415">Dosya satır sonlarını değiştirmek için SHA-256 karmaları değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-415">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="8b7cb-416">Bu sorundan kaçınmak için, [ `.gitattributes` Yapı yapıtlarını `binary` dosya olarak değerlendirmek üzere](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-416">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="8b7cb-417">Web sunucusu, bu dosyaları sunma bölümünde dosya içeriğini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-417">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="8b7cb-418">Örneğin, bazı içerik dağıtım ağları (CDNs) otomatik olarak HTML 'yi [küçültmeye](xref:client-side/bundling-and-minification#minification) çalışır ve bu sayede onu değiştirir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-418">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="8b7cb-419">Bu tür özellikleri devre dışı bırakmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-419">You may need to disable such features.</span></span>

<span data-ttu-id="8b7cb-420">Bu durumun hangi durumlarda geçerli olduğunu tanılamak için:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-420">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="8b7cb-421">Hatanın hata iletisini okuyarak tetikleneceği dosyayı aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-421">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="8b7cb-422">Tarayıcınızın geliştirici araçlarını açın ve *ağ* sekmesine bakın. Gerekirse, istek ve yanıtların listesini görmek için sayfayı yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-422">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="8b7cb-423">Bu listedeki hatayı tetikleyen dosyayı bulun.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-423">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="8b7cb-424">Yanıttaki HTTP durum kodunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-424">Check the HTTP status code in the response.</span></span> <span data-ttu-id="8b7cb-425">Sunucu *200-Tamam* (veya başka bir 2xx durum kodu) dışında bir şey döndürürse, tanılamaya yönelik bir sunucu tarafı sorununuz vardır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-425">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="8b7cb-426">Örneğin, 403 durum kodu bir yetkilendirme sorunu olduğu anlamına gelirken durum kodu 500, sunucunun belirtilmeyen bir şekilde başarısız olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-426">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="8b7cb-427">Uygulamayı tanılamak ve onarmak için sunucu tarafı günlüklerine başvurun.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-427">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="8b7cb-428">Durum kodu kaynak için *200-Tamam* ise, tarayıcının geliştirici araçlarındaki yanıt içeriğine bakın ve içeriğin beklenen verilerle uyumlu olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-428">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="8b7cb-429">Örneğin, yaygın bir sorun, isteklerin `index.html` diğer dosyalar için bile verilerinizi döndürmesi için yönlendirmeyi yanlış yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-429">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="8b7cb-430">`.wasm`İsteklerin yanıtlarının, Weelsembly ikilileriyle ve `.dll` isteklerin yanıtlarının .NET Derleme ikili dosyalarına sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-430">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="8b7cb-431">Aksi takdirde, tanılamaya yönelik bir sunucu tarafı yönlendirme sorununuz vardır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-431">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="8b7cb-432">Sunucunun sürekli doğru verileri döndürdüğünü onaylamak için, dosyanın oluşturulması ve teslimi arasındaki içerikleri değiştirmek için başka bir şey olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-432">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="8b7cb-433">Bunu araştırmak için:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-433">To investigate this:</span></span>

 * <span data-ttu-id="8b7cb-434">Dosyalar derlendikten sonra dosyaları değiştirirken derleme araç zinciri ve dağıtım mekanizmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-434">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="8b7cb-435">Git 'in, daha önce açıklandığı gibi dosya satır sonlarını dönüştürerken buna bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-435">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="8b7cb-436">Yanıtları dinamik olarak değiştirmek üzere ayarlanmış olmaları durumunda Web sunucusunu veya CDN yapılandırmasını inceleyin (örneğin, HTML 'yi küçültmeye çalışırken).</span><span class="sxs-lookup"><span data-stu-id="8b7cb-436">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="8b7cb-437">Web sunucusunun HTTP sıkıştırması uygulaması (örneğin, döndürme `content-encoding: br` veya döndürme `content-encoding: gzip` ), bu nedenle bu, sıkıştırmayı açma işleminden sonra sonucu etkilemez.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-437">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="8b7cb-438">Ancak, Web sunucusunun sıkıştırılmamış verileri değiştirmesi iyi *değildir* .</span><span class="sxs-lookup"><span data-stu-id="8b7cb-438">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="8b7cb-439">PWA olmayan uygulamalar için bütünlük denetimini devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="8b7cb-439">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="8b7cb-440">Çoğu durumda, bütünlük denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-440">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="8b7cb-441">Bütünlük denetimini devre dışı bırakmak, beklenmeyen yanıtlara neden olan temeldeki sorunu çözmez ve daha önce listelenen avantajları kaybetme sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-441">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="8b7cb-442">Web sunucusunun tutarlı yanıtlar döndürecek şekilde güvenmeme, ancak hiçbir seçim olmadığı ancak bütünlük denetimlerini devre dışı bırakabileceği durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-442">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="8b7cb-443">Bütünlük denetimlerini devre dışı bırakmak için aşağıdakini proje dosyasındaki bir özellik grubuna ekleyin :::no-loc(Blazor WebAssembly)::: `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="8b7cb-443">To disable integrity checks, add the following to a property group in the :::no-loc(Blazor WebAssembly)::: project's `.csproj` file:</span></span>

```xml
<:::no-loc(Blazor):::CacheBootResources>false</:::no-loc(Blazor):::CacheBootResources>
```

<span data-ttu-id="8b7cb-444">`:::no-loc(Blazor):::CacheBootResources`:::no-loc(Blazor)::: `.dll` , `.wasm` özelliği SHA-256 karmalarını temel alarak,, ve diğer dosyaları önbelleğe almanın varsayılan davranışını devre dışı bırakır çünkü özellik SHA-256 karmalarını doğruluk açısından güvenemediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-444">`:::no-loc(Blazor):::CacheBootResources` also disables :::no-loc(Blazor):::'s default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="8b7cb-445">Bu ayar ile birlikte, tarayıcının normal HTTP önbelleği bu dosyaları önbelleğe almaya devam edebilir, ancak bunun gerçekleşmediğine bakılmaksızın Web sunucusu yapılandırmanıza ve `cache-control` hizmet verdiği üstbilgilere göre değişir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-445">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="8b7cb-446">`:::no-loc(Blazor):::CacheBootResources`Özelliği, [aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)için bütünlük denetimlerini devre dışı bırakmıyor.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-446">The `:::no-loc(Blazor):::CacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="8b7cb-447">PWAs ile ilgili rehberlik için [PWAs için bütünlük denetimini devre dışı bırakma](#disable-integrity-checking-for-pwas) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-447">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="8b7cb-448">PWAs için bütünlük denetimini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="8b7cb-448">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="8b7cb-449">:::no-loc(Blazor):::Aşamalı Web uygulaması (PWA) şablonu, `service-worker.published.js` çevrimdışı kullanım için uygulama dosyalarını getirmekten ve saklamaktan sorumlu önerilen bir dosya içerir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-449">:::no-loc(Blazor):::'s Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="8b7cb-450">Bu, normal uygulama başlangıç mekanizmasından ayrı bir işlemdir ve kendi ayrı bütünlük denetimi mantığına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-450">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="8b7cb-451">Dosya içinde `service-worker.published.js` Aşağıdaki satır mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-451">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="8b7cb-452">Bütünlük denetimini devre dışı bırakmak için, `integrity` satırı aşağıdaki şekilde değiştirerek parametreyi kaldırın:</span><span class="sxs-lookup"><span data-stu-id="8b7cb-452">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="8b7cb-453">Daha sonra, bütünlük denetimini devre dışı bırakmak, bütünlük denetimi tarafından sunulan güvenlik garantisi kaybetmeniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-453">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="8b7cb-454">Örneğin, kullanıcının tarayıcısı uygulamayı yeni bir sürümünü dağıttığınız andan itibaren önbelleğe alıyorsa, eski dağıtımdan ve bazıları yeni dağıtımdan bazı dosyaları önbelleğe alabilir, bu da bir risk vardır.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-454">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="8b7cb-455">Bu durumda, daha fazla güncelleştirme dağıtana kadar uygulama bozuk bir durumda takılmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="8b7cb-455">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
