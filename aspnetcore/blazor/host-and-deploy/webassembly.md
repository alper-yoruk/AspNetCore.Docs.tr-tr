---
title: ASP.NET Core barındırma ve dağıtma Blazor WebAssembly
author: guardrex
description: BlazorASP.NET Core, Içerik teslim ağları (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7edba338716a0545390ec53775f69eaef141d389
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855293"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="331b1-103">ASP.NET Core barındırma ve dağıtma Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="331b1-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="331b1-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından</span><span class="sxs-lookup"><span data-stu-id="331b1-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="331b1-105">[ Blazor WebAssembly Barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="331b1-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="331b1-106">BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="331b1-107">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="331b1-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="331b1-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="331b1-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="331b1-109">BlazorUygulama, bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="331b1-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="331b1-110">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="331b1-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="331b1-111">BlazorUygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz Blazor .</span><span class="sxs-lookup"><span data-stu-id="331b1-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="331b1-112">Bu strateji, bir uygulamayı IIS alt uygulaması olarak barındırma hakkında bilgi içeren [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="331b1-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="331b1-113">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="331b1-113">Compression</span></span>

<span data-ttu-id="331b1-114">Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="331b1-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="331b1-115">Aşağıdaki sıkıştırma algoritmaları kullanılır:</span><span class="sxs-lookup"><span data-stu-id="331b1-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="331b1-116">[Brotli](https://tools.ietf.org/html/rfc7932) (en yüksek düzey)</span><span class="sxs-lookup"><span data-stu-id="331b1-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="331b1-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="331b1-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="331b1-118">Blazor , uygun sıkıştırılmış dosyaları sunacak ana bilgisayarı kullanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="331b1-119">ASP.NET Core barındırılan bir proje kullanırken, konak proje içerik anlaşması yapabilir ve statik olarak sıkıştırılmış dosyalara hizmet verebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="331b1-120">Blazor WebAssemblyTek başına bir uygulamayı barındırırken, statik olarak sıkıştırılmış dosyaların sunulmasını sağlamak için ek çalışma gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="331b1-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="331b1-121">IIS `web.config` sıkıştırma yapılandırması Için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="331b1-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="331b1-122">GitHub sayfaları gibi statik olarak sıkıştırılmış dosya içeriği anlaşmasını desteklemeyen statik barındırma çözümlerinde barındırırken, Brotli sıkıştırılan dosyaları getirmek ve kodunu çözmek üzere uygulamayı yapılandırmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="331b1-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="331b1-123">[Google/Brotli GitHub deposundan](https://github.com/google/brotli)JavaScript Brotli kod çözücüsünü edinin.</span><span class="sxs-lookup"><span data-stu-id="331b1-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="331b1-124">2020 Eylül itibariyle, kod çözücü dosyasının adı `decode.js` ve deponun [ `js` klasöründe](https://github.com/google/brotli/tree/master/js)bulunur.</span><span class="sxs-lookup"><span data-stu-id="331b1-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="331b1-125">`decode.js` `decode.min.js` [Google/brotli GitHub deposundaki](https://github.com/google/brotli)betiğin () küçültülmüş sürümünde bir gerileme bulunur.</span><span class="sxs-lookup"><span data-stu-id="331b1-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="331b1-126">Betiği kendinize göre küçültün veya sorun penceresi ne kadar [NPM paketini](https://www.npmjs.com/package/brotli) kullanın [. Brotlişifre çözme decode.min.js (google/brotli #844) çözümlenmez](https://github.com/google/brotli/issues/844) .</span><span class="sxs-lookup"><span data-stu-id="331b1-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="331b1-127">Bu bölümdeki örnek kod, betiğin **küçültülmüş** sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="331b1-128">Kod çözücüyü kullanmak için uygulamayı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-128">Update the app to use the decoder.</span></span> <span data-ttu-id="331b1-129">İçindeki kapanış etiketinin içindeki biçimlendirmeyi `<body>` aşağıdaki gibi değiştirin `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="331b1-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="331b1-130">Sıkıştırmayı devre dışı bırakmak için `BlazorEnableCompression` uygulamanın proje dosyasına MSBuild özelliğini ekleyin ve değeri şu şekilde ayarlayın `false` :</span><span class="sxs-lookup"><span data-stu-id="331b1-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="331b1-131">`BlazorEnableCompression`Özelliği [`dotnet publish`](/dotnet/core/tools/dotnet-publish) komut kabuğunda aşağıdaki söz dizimi ile komuta geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="331b1-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="331b1-132">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="331b1-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="331b1-133">Bir uygulamadaki sayfa bileşenlerine yönelik yönlendirme istekleri Blazor WebAssembly , barındırılan bir uygulamadaki yönlendirme istekleri kadar basittir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="331b1-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="331b1-134">Blazor WebAssemblyİki bileşeni olan bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="331b1-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="331b1-135">`Main.razor`: Uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .</span><span class="sxs-lookup"><span data-stu-id="331b1-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="331b1-136">`About.razor`: `About` bileşen.</span><span class="sxs-lookup"><span data-stu-id="331b1-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="331b1-137">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="331b1-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="331b1-138">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="331b1-138">The browser makes a request.</span></span>
1. <span data-ttu-id="331b1-139">Varsayılan sayfa döndürülür, genellikle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="331b1-140">`index.html` uygulamayı önyükleme.</span><span class="sxs-lookup"><span data-stu-id="331b1-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="331b1-141">Blazoruygulamasının yönlendirici yüklenir ve Razor `Main` bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="331b1-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="331b1-142">Ana sayfada, `About` Blazor yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="331b1-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="331b1-143">*Blazor WebAssembly Uygulamadaki* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="331b1-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="331b1-144">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="331b1-144">The router handles the requests internally.</span></span>

<span data-ttu-id="331b1-145">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="331b1-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="331b1-146">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="331b1-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="331b1-147">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri sunucuda fiziksel olarak olmayan tüm kaynak isteklerini sayfaya yeniden yazması gerekir `index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="331b1-148">`index.html`Döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="331b1-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="331b1-149">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayımlanan dosyasıyla birlikte kullanabilirsiniz `web.config` .</span><span class="sxs-lookup"><span data-stu-id="331b1-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="331b1-150">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="331b1-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="331b1-151">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="331b1-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="331b1-152">*Barındırılan bir dağıtım* , Blazor WebAssembly uygulamayı bir Web sunucusu üzerinde çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılarına sunar.</span><span class="sxs-lookup"><span data-stu-id="331b1-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="331b1-153">İstemci uygulaması, sunucu uygulamasının Blazor WebAssembly `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` diğer statik Web varlıklarıyla birlikte sunucu uygulamasının klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="331b1-154">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="331b1-154">The two apps are deployed together.</span></span> <span data-ttu-id="331b1-155">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="331b1-156">Barındırılan bir dağıtım için, Visual Studio **Blazor WebAssembly uygulama** proje şablonunu (komutunu kullanırken `blazorwasm` şablon [`dotnet new`](/dotnet/core/tools/dotnet-new) ), **`Hosted`** seçeneği belirlendiğinde ( `-ho|--hosted` `dotnet new` komutunu kullanırken) içerir.</span><span class="sxs-lookup"><span data-stu-id="331b1-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="331b1-157">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="331b1-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="331b1-158">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="331b1-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="331b1-159">Birden çok uygulama ile barındırılan dağıtım Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="331b1-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="331b1-160">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="331b1-160">App configuration</span></span>

<span data-ttu-id="331b1-161">Barındırılan bir Blazor çözümü birden çok uygulamaya yönelik olarak yapılandırmak için Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="331b1-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="331b1-162">BlazorBarındırılan proje şablonundan mevcut bir barındırılan çözümü kullanın veya yeni bir çözüm oluşturun Blazor .</span><span class="sxs-lookup"><span data-stu-id="331b1-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="331b1-163">İstemci uygulamasının proje dosyasında, `<StaticWebAssetBasePath>` `<PropertyGroup>` `FirstApp` projenin statik varlıkların temel yolunu ayarlamak için değeri ile öğesine bir özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="331b1-164">Çözüme ikinci bir istemci uygulaması ekleyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="331b1-165">Çözümün klasörüne adlı bir klasör ekleyin `SecondClient` .</span><span class="sxs-lookup"><span data-stu-id="331b1-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="331b1-166">Blazor WebAssembly `SecondBlazorApp.Client` `SecondClient` Proje şablonundan klasöründe adlı bir uygulama oluşturun Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="331b1-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="331b1-167">Uygulamanın proje dosyasında:</span><span class="sxs-lookup"><span data-stu-id="331b1-167">In the app's project file:</span></span>

    * <span data-ttu-id="331b1-168">`<StaticWebAssetBasePath>`Değerine sahip öğesine bir özelliği ekleyin `<PropertyGroup>` `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="331b1-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="331b1-169">Projeye bir proje başvurusu ekleyin `Shared` :</span><span class="sxs-lookup"><span data-stu-id="331b1-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="331b1-170">Yer tutucu `{SOLUTION NAME}` çözümün adıdır.</span><span class="sxs-lookup"><span data-stu-id="331b1-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="331b1-171">Sunucu uygulamasının proje dosyasında, eklenen istemci uygulaması için bir proje başvurusu oluşturun:</span><span class="sxs-lookup"><span data-stu-id="331b1-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="331b1-172">Sunucu uygulamasının `Properties/launchSettings.json` dosyasında, `applicationUrl` `{SOLUTION NAME}.Server` 5001 ve 5002 bağlantı noktalarında istemci uygulamalarına erişmek için Kestrel profili () öğesini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="331b1-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="331b1-173">Sunucu uygulamasının `Startup.Configure` yönteminde ( `Startup.cs` ), çağrısından sonra görünen aşağıdaki satırları kaldırın <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="331b1-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="331b1-174">İstekleri istemci uygulamalarına eşleyen ara yazılım ekleme.</span><span class="sxs-lookup"><span data-stu-id="331b1-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="331b1-175">Aşağıdaki örnek, şu durumlarda ara yazılımı çalıştıracak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="331b1-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="331b1-176">İstek bağlantı noktası, özgün istemci uygulaması için 5001 ya da eklenen istemci uygulaması için 5002 ' dir.</span><span class="sxs-lookup"><span data-stu-id="331b1-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="331b1-177">İstek Konağı, `firstapp.com` özgün istemci uygulaması ya da `secondapp.com` eklenen istemci uygulaması için.</span><span class="sxs-lookup"><span data-stu-id="331b1-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="331b1-178">Bu bölümde gösterilen örnekte, için ek yapılandırma gerekir:</span><span class="sxs-lookup"><span data-stu-id="331b1-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="331b1-179">Örnek ana bilgisayar etki alanlarındaki uygulamalara erişme `firstapp.com` ve `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="331b1-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="331b1-180">TLS güvenliğini etkinleştirmek için istemci uygulamalarına yönelik Sertifikalar (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="331b1-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="331b1-181">Gerekli yapılandırma Bu makalenin kapsamı dışındadır ve çözümün nasıl barındırıldığını bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="331b1-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="331b1-182">Daha fazla bilgi için bkz. [konak ve dağıtım makaleleri](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="331b1-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="331b1-183">Aşağıdaki kodu satırların daha önce kaldırıldığı yere yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="331b1-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
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

      second.UseBlazorFrameworkFiles("/SecondApp");
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

* <span data-ttu-id="331b1-184">Sunucu uygulamasının Hava durumu tahmin denetleyicisi 'nde ( `Controllers/WeatherForecastController.cs` ), var olan yolu ( `[Route("[controller]")]` ) `WeatherForecastController` aşağıdaki yollarla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="331b1-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="331b1-185">Sunucu uygulamasının yöntemine eklenen ara yazılım, `Startup.Configure` gelen istekleri `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` bağlantı noktasına (5001/5002) veya etki alanına () bağlı olarak ya da öğesine göre değiştirir `firstapp.com` / `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="331b1-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="331b1-186">Sunucu uygulamasından istemci uygulamalarına Hava durumu verilerini döndürmek için önceki denetleyici yolları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="331b1-187">Statik varlıklar ve sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="331b1-187">Static assets and class libraries</span></span>

<span data-ttu-id="331b1-188">Statik varlıklar için aşağıdaki yaklaşımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="331b1-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="331b1-189">Varlık, istemci uygulamasının `wwwroot` klasöründe olduğunda, kendi yollarını normal olarak sağlayın:</span><span class="sxs-lookup"><span data-stu-id="331b1-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="331b1-190">Varlık `wwwroot` bir [ Razor sınıf kitaplığı (RCL)](xref:blazor/components/class-libraries)klasöründe olduğunda, [RCL makalesindeki](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)kılavuzluk uyarınca istemci uygulamasındaki statik varlığa başvurun:</span><span class="sxs-lookup"><span data-stu-id="331b1-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

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

<span data-ttu-id="331b1-191">Bir sınıf kitaplığı tarafından bir istemci uygulamasına verilen bileşenlere normal olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="331b1-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="331b1-192">Herhangi bir bileşen stil sayfaları veya JavaScript dosyaları gerektiriyorsa, istemci uygulamanın `wwwroot/index.html` dosyası doğru statik varlık bağlantılarını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-192">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="331b1-193">Bu yaklaşımlar aşağıdaki örneklerde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="331b1-193">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="331b1-194">Aşağıdaki `Jeep` bileşeni istemci uygulamalarından birine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="331b1-194">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="331b1-195">`Jeep`Bileşen şunları kullanır:</span><span class="sxs-lookup"><span data-stu-id="331b1-195">The `Jeep` component uses:</span></span>

* <span data-ttu-id="331b1-196">İstemci uygulamanın `wwwroot` klasöründen () bir resim `jeep-cj.png` .</span><span class="sxs-lookup"><span data-stu-id="331b1-196">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="331b1-197">[Eklenen Razor Bileşen kitaplığı](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` klasöründen () bir resim `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="331b1-197">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="331b1-198">Örnek bileşen ( `Component1` ), kitaplık çözüme eklendiğinde RCL proje şablonu tarafından otomatik olarak oluşturulur `JeepImage` .</span><span class="sxs-lookup"><span data-stu-id="331b1-198">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="331b1-199">Görüntülerin sahibi olmadığınız takdirde, taşıtlar görüntülerini **herkese yayımlamayın** .</span><span class="sxs-lookup"><span data-stu-id="331b1-199">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="331b1-200">Aksi takdirde, telif hakkı ihlali riski vardır.</span><span class="sxs-lookup"><span data-stu-id="331b1-200">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
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

<span data-ttu-id="331b1-201">Kitaplığın `jeep-yj.png` görüntüsü kitaplığın bileşenine de eklenebilir `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="331b1-201">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="331b1-202">İstemci uygulamasının dosyası, `wwwroot/index.html` aşağıdaki eklenmiş etikete sahip kitaplığın stil sayfasını ister `<link>` :</span><span class="sxs-lookup"><span data-stu-id="331b1-202">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="331b1-203">`Jeep`İstemci uygulamasının bileşeninde bileşene gezinti ekleyin `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="331b1-203">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="331b1-204">RCLs hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="331b1-204">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="331b1-205">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="331b1-205">Standalone deployment</span></span>

<span data-ttu-id="331b1-206">*Tek başına dağıtım* , Blazor WebAssembly uygulamaya doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="331b1-206">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="331b1-207">Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-207">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="331b1-208">Tek başına dağıtım varlıkları `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-208">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="331b1-209">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="331b1-209">Azure App Service</span></span>

<span data-ttu-id="331b1-210">Blazor WebAssembly uygulamalar, uygulamayı [IIS](#iis)'de barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-210">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="331b1-211">Tek başına bir Blazor WebAssembly uygulamanın Linux için Azure App Service dağıtımı şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="331b1-211">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="331b1-212">Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="331b1-212">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="331b1-213">Bu senaryoyu etkinleştirmek için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="331b1-213">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="331b1-214">IIS</span><span class="sxs-lookup"><span data-stu-id="331b1-214">IIS</span></span>

<span data-ttu-id="331b1-215">IIS, uygulamalar için özellikli bir statik dosya sunucusudur Blazor .</span><span class="sxs-lookup"><span data-stu-id="331b1-215">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="331b1-216">IIS 'yi barındıracak şekilde yapılandırmak için Blazor bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="331b1-216">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="331b1-217">Yayımlanan varlıklar `/bin/Release/{TARGET FRAMEWORK}/publish` klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="331b1-217">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="331b1-218">`publish`Web sunucusunda veya barındırma hizmetinde klasörün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="331b1-218">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="331b1-219">web.config</span><span class="sxs-lookup"><span data-stu-id="331b1-219">web.config</span></span>

<span data-ttu-id="331b1-220">Bir Blazor Proje yayımlandığında, `web.config` aşağıdaki IIS yapılandırmasıyla bir dosya oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="331b1-220">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="331b1-221">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="331b1-221">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="331b1-222">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="331b1-222">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="331b1-223">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="331b1-223">`.json`: `application/json`</span></span>
  * <span data-ttu-id="331b1-224">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="331b1-224">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="331b1-225">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="331b1-225">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="331b1-226">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="331b1-226">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="331b1-227">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="331b1-227">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="331b1-228">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="331b1-228">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="331b1-229">Uygulamanın statik varlıklarının bulunduğu alt dizini ( `wwwroot/{PATH REQUESTED}` ) sunar.</span><span class="sxs-lookup"><span data-stu-id="331b1-229">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="331b1-230">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe () uygulamanın varsayılan belgesine yeniden yönlendirilmesi için SPA geri dönüş yönlendirmesi oluşturun `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-230">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="331b1-231">Özel bir web.config kullanma</span><span class="sxs-lookup"><span data-stu-id="331b1-231">Use a custom web.config</span></span>

<span data-ttu-id="331b1-232">Özel bir dosya kullanmak için `web.config` , özel `web.config` dosyayı proje klasörünün köküne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-232">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="331b1-233">Projeyi uygulamanın proje dosyasında kullanarak IIS 'e özgü varlıkları yayımlayacak şekilde yapılandırın `PublishIISAssets` ve projeyi yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="331b1-233">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="331b1-234">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="331b1-234">Install the URL Rewrite Module</span></span>

<span data-ttu-id="331b1-235">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-235">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="331b1-236">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="331b1-236">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="331b1-237">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-237">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="331b1-238">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="331b1-238">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="331b1-239">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="331b1-239">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="331b1-240">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="331b1-240">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="331b1-241">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="331b1-241">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="331b1-242">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="331b1-242">Copy the installer to the server.</span></span> <span data-ttu-id="331b1-243">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="331b1-243">Run the installer.</span></span> <span data-ttu-id="331b1-244">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="331b1-244">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="331b1-245">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="331b1-245">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="331b1-246">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="331b1-246">Configure the website</span></span>

<span data-ttu-id="331b1-247">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="331b1-247">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="331b1-248">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="331b1-248">The folder contains:</span></span>

* <span data-ttu-id="331b1-249">`web.config`Web sitesini yapılandırmak için gereken yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS tarafından kullanılan dosya.</span><span class="sxs-lookup"><span data-stu-id="331b1-249">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="331b1-250">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="331b1-250">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="331b1-251">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="331b1-251">Host as an IIS sub-app</span></span>

<span data-ttu-id="331b1-252">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="331b1-252">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="331b1-253">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="331b1-253">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="331b1-254">Blazor `web.config` Dosyaya bir bölüm ekleyerek uygulamanın yayımlanmış dosyasındaki işleyiciyi kaldırın `<handlers>` :</span><span class="sxs-lookup"><span data-stu-id="331b1-254">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="331b1-255">Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :</span><span class="sxs-lookup"><span data-stu-id="331b1-255">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="331b1-256">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:blazor/host-and-deploy/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-256">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="331b1-257">Uygulamanın dosyasındaki uygulama temel yolunu, `index.html` IIS 'de alt uygulamayı yapılandırırken kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="331b1-257">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="331b1-258">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="331b1-258">Brotli and Gzip compression</span></span>

<span data-ttu-id="331b1-259">*Bu bölüm yalnızca tek başına uygulamalar için geçerlidir Blazor WebAssembly . barındırılan Blazor uygulamalar `web.config` , bu bölümde bağlantılı dosyayı değil, varsayılan bir ASP.NET Core uygulama dosyası kullanır.*</span><span class="sxs-lookup"><span data-stu-id="331b1-259">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="331b1-260">IIS, `web.config` Blazor tek başına uygulamalar için Brotli veya gzip ile sıkıştırılmış varlıklar sunacak şekilde yapılandırılabilir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="331b1-260">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="331b1-261">Örnek bir yapılandırma dosyası için bkz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) ..</span><span class="sxs-lookup"><span data-stu-id="331b1-261">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="331b1-262">Örnek dosyanın ek yapılandırması `web.config` aşağıdaki senaryolarda gerekli olabilir:</span><span class="sxs-lookup"><span data-stu-id="331b1-262">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="331b1-263">Uygulamanın belirtimi aşağıdakilerden birini çağırır:</span><span class="sxs-lookup"><span data-stu-id="331b1-263">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="331b1-264">Örnek dosya tarafından yapılandırılmayan sıkıştırılmış dosyalara hizmet sunma `web.config` .</span><span class="sxs-lookup"><span data-stu-id="331b1-264">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="331b1-265">Sıkıştırılmış dosyalara, sıkıştırılmamış biçimde örnek dosya tarafından yapılandırılmış olarak sunma `web.config` .</span><span class="sxs-lookup"><span data-stu-id="331b1-265">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="331b1-266">Sunucunun IIS yapılandırması (örneğin, `applicationHost.config` ) sunucu DÜZEYINDE IIS Varsayılanları sağlar.</span><span class="sxs-lookup"><span data-stu-id="331b1-266">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="331b1-267">Sunucu düzeyindeki yapılandırmaya bağlı olarak, uygulama örnek dosyanın içerenden farklı bir IIS yapılandırması gerektirebilir `web.config` .</span><span class="sxs-lookup"><span data-stu-id="331b1-267">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="331b1-268">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="331b1-268">Troubleshooting</span></span>

<span data-ttu-id="331b1-269">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="331b1-269">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="331b1-270">Modül yüklü olmadığında, `web.config` Dosya IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="331b1-270">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="331b1-271">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller Blazor .</span><span class="sxs-lookup"><span data-stu-id="331b1-271">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="331b1-272">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="331b1-272">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="331b1-273">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="331b1-273">Azure Storage</span></span>

<span data-ttu-id="331b1-274">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="331b1-274">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="331b1-275">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="331b1-275">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="331b1-276">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="331b1-276">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="331b1-277">**Dizin belgesi adını** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-277">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="331b1-278">**Hata belge yolunu** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-278">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="331b1-279">Razor bileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="331b1-279">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="331b1-280">Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında Blazor , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna** yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="331b1-280">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="331b1-281">`index.html`BLOB döndürülür ve Blazor yönlendirici yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="331b1-281">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="331b1-282">Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="331b1-282">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="331b1-283">Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="331b1-283">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="331b1-284">`Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-284">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="331b1-285">Her dosya için Depolama Gezgini (Azure portal):</span><span class="sxs-lookup"><span data-stu-id="331b1-285">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="331b1-286">Dosyaya sağ tıklayın ve **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="331b1-286">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="331b1-287">**ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="331b1-287">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="331b1-288">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="331b1-288">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="331b1-289">Nginx</span><span class="sxs-lookup"><span data-stu-id="331b1-289">Nginx</span></span>

<span data-ttu-id="331b1-290">Aşağıdaki `nginx.conf` Dosya, NGINX 'in `index.html` diskteki karşılık gelen bir dosyayı bulamadığı her seferinde dosyayı göndermek üzere nasıl yapılandırılacağını göstermek için basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="331b1-290">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="331b1-291">[NGINX veri bloğu hızı sınırı](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) ile ayarlandığında [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , Blazor WebAssembly uygulamalar `burst` bir uygulama tarafından yapılan görece çok sayıda isteği karşılamak için büyük bir parametre değeri gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-291">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="331b1-292">Başlangıçta değeri en az 60 olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="331b1-292">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="331b1-293">Tarayıcı geliştirici araçları veya ağ trafiği Aracı, isteklerin *503 servis dışı* bir durum kodu aldığını gösteriyorsa değeri artırın.</span><span class="sxs-lookup"><span data-stu-id="331b1-293">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="331b1-294">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="331b1-294">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="331b1-295">Docker 'da NGINX</span><span class="sxs-lookup"><span data-stu-id="331b1-295">Nginx in Docker</span></span>

<span data-ttu-id="331b1-296">BlazorNGINX kullanarak Docker 'da barındırmak Için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="331b1-296">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="331b1-297">Dockerfile dosyasını, dosyayı kapsayıcıya kopyalamak için güncelleştirin `nginx.config` .</span><span class="sxs-lookup"><span data-stu-id="331b1-297">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="331b1-298">Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-298">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="331b1-299">Apache</span><span class="sxs-lookup"><span data-stu-id="331b1-299">Apache</span></span>

<span data-ttu-id="331b1-300">Bir Blazor WebAssembly uygulamayı CentOS 7 veya sonraki bir sürüme dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="331b1-300">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="331b1-301">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="331b1-301">Create the Apache configuration file.</span></span> <span data-ttu-id="331b1-302">Aşağıdaki örnek, Basitleştirilmiş bir yapılandırma dosyasıdır ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="331b1-302">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="331b1-303">Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-303">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="331b1-304">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).</span><span class="sxs-lookup"><span data-stu-id="331b1-304">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="331b1-305">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="331b1-305">Restart the Apache service.</span></span>

<span data-ttu-id="331b1-306">Daha fazla bilgi için bkz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) . ve.</span><span class="sxs-lookup"><span data-stu-id="331b1-306">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="331b1-307">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="331b1-307">GitHub Pages</span></span>

<span data-ttu-id="331b1-308">URL yeniden işlemesini işlemek için, `wwwroot/404.html` isteği sayfaya yönlendirmeyi işleyen bir betiği olan bir dosya ekleyin `index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-308">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="331b1-309">Bir örnek için bkz. [Stevesandersonms/ Blazor ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="331b1-309">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="331b1-310">[Canlı site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="331b1-310">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="331b1-311">Bir kuruluş sitesi yerine bir proje sitesi kullanırken, `<base>` içindeki etiketini güncelleştirin `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="331b1-311">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="331b1-312">`href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin, `/my-repository/` ) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="331b1-312">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="331b1-313">[Stevesandersonms/ Blazor ongithubpages GitHub deposunda](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), temel `href` [ `.github/workflows/main.yml` yapılandırma dosyası](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)tarafından yayımlama sırasında güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-313">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="331b1-314">[Stevesandersonms/ Blazor ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) , .net Foundation veya Microsoft tarafından sahip değil, korunmuyor veya desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="331b1-314">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="331b1-315">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="331b1-315">Host configuration values</span></span>

<span data-ttu-id="331b1-316">[ Blazor WebAssembly uygulamalar](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında komut satırı bağımsız değişkenleri olarak aşağıdaki ana bilgisayar yapılandırma değerlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-316">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="331b1-317">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="331b1-317">Content root</span></span>

<span data-ttu-id="331b1-318">`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="331b1-318">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="331b1-319">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="331b1-319">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="331b1-320">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-320">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="331b1-321">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="331b1-321">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="331b1-322">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="331b1-322">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="331b1-323">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="331b1-323">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="331b1-324">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="331b1-324">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="331b1-325">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="331b1-325">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="331b1-326">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="331b1-326">Path base</span></span>

<span data-ttu-id="331b1-327">`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="331b1-327">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="331b1-328">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="331b1-328">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="331b1-329">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="331b1-329">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="331b1-330">Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` .</span><span class="sxs-lookup"><span data-stu-id="331b1-330">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="331b1-331">Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-331">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="331b1-332">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-332">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="331b1-333">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="331b1-333">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="331b1-334">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="331b1-334">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="331b1-335">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="331b1-335">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="331b1-336">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="331b1-336">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="331b1-337">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="331b1-337">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="331b1-338">URL’ler</span><span class="sxs-lookup"><span data-stu-id="331b1-338">URLs</span></span>

<span data-ttu-id="331b1-339">`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="331b1-339">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="331b1-340">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-340">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="331b1-341">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="331b1-341">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="331b1-342">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="331b1-342">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="331b1-343">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="331b1-343">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="331b1-344">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="331b1-344">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="331b1-345">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="331b1-345">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="331b1-346">Kesiciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="331b1-346">Configure the Trimmer</span></span>

<span data-ttu-id="331b1-347">Blazor çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) kırpılmasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="331b1-347">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="331b1-348">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="331b1-348">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="331b1-349">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="331b1-349">Configure the Linker</span></span>

<span data-ttu-id="331b1-350">Blazor çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="331b1-350">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="331b1-351">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="331b1-351">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="331b1-352">Özel önyükleme kaynağı yükleme</span><span class="sxs-lookup"><span data-stu-id="331b1-352">Custom boot resource loading</span></span>

<span data-ttu-id="331b1-353">Blazor WebAssemblyUygulama, `loadBootResource` yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için işleviyle başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-353">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="331b1-354">`loadBootResource`Aşağıdaki senaryolar için kullanın:</span><span class="sxs-lookup"><span data-stu-id="331b1-354">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="331b1-355">Kullanıcıların saat dilimi verileri veya bir CDN 'den statik kaynakları yüklemesine izin verin `dotnet.wasm` .</span><span class="sxs-lookup"><span data-stu-id="331b1-355">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="331b1-356">Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="331b1-356">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="331b1-357">Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .</span><span class="sxs-lookup"><span data-stu-id="331b1-357">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="331b1-358">`loadBootResource` Parametreler aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="331b1-358">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="331b1-359">Parametre</span><span class="sxs-lookup"><span data-stu-id="331b1-359">Parameter</span></span>    | <span data-ttu-id="331b1-360">Açıklama</span><span class="sxs-lookup"><span data-stu-id="331b1-360">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="331b1-361">Kaynağın türü.</span><span class="sxs-lookup"><span data-stu-id="331b1-361">The type of the resource.</span></span> <span data-ttu-id="331b1-362">İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="331b1-362">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="331b1-363">Kaynağın adı.</span><span class="sxs-lookup"><span data-stu-id="331b1-363">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="331b1-364">Kaynağın göreli veya mutlak URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="331b1-364">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="331b1-365">Yanıtta beklenen içeriği temsil eden bütünlük dizesi.</span><span class="sxs-lookup"><span data-stu-id="331b1-365">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="331b1-366">`loadBootResource` yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:</span><span class="sxs-lookup"><span data-stu-id="331b1-366">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="331b1-367">URI dizesi.</span><span class="sxs-lookup"><span data-stu-id="331b1-367">URI string.</span></span> <span data-ttu-id="331b1-368">Aşağıdaki örnekte ( `wwwroot/index.html` ), aşağıdaki dosyalar ' de BIR CDN 'den sunulur `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="331b1-368">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="331b1-369">Saat dilimi verileri</span><span class="sxs-lookup"><span data-stu-id="331b1-369">Timezone data</span></span>

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

* <span data-ttu-id="331b1-370">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="331b1-370">`Promise<Response>`.</span></span> <span data-ttu-id="331b1-371">`integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-371">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="331b1-372">Aşağıdaki örnek ( `wwwroot/index.html` ) giden isteklere özel bır http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:</span><span class="sxs-lookup"><span data-stu-id="331b1-372">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="331b1-373">`null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.</span><span class="sxs-lookup"><span data-stu-id="331b1-373">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="331b1-374">Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-374">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="331b1-375">CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="331b1-375">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="331b1-376">Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-376">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="331b1-377">İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="331b1-377">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="331b1-378">DLL dosyalarının dosya adı uzantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="331b1-378">Change the filename extension of DLL files</span></span>

<span data-ttu-id="331b1-379">Uygulamanın yayımlanmış dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa `.dll` , bu bölümdeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="331b1-379">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="331b1-380">Uygulamayı yayımladıktan sonra, `.dll` farklı bir dosya uzantısı kullanmak üzere dosyaları yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın.</span><span class="sxs-lookup"><span data-stu-id="331b1-380">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="331b1-381">`.dll` `wwwroot` Uygulamanın yayınlanan çıktısının dizinindeki dosyaları hedefleyin (örneğin, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="331b1-381">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="331b1-382">Aşağıdaki örneklerde `.dll` Dosyalar dosya uzantısını kullanacak şekilde yeniden adlandırılır `.bin` .</span><span class="sxs-lookup"><span data-stu-id="331b1-382">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="331b1-383">Windows'da:</span><span class="sxs-lookup"><span data-stu-id="331b1-383">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="331b1-384">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-384">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="331b1-385">Linux veya macOS 'ta:</span><span class="sxs-lookup"><span data-stu-id="331b1-385">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="331b1-386">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-386">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="331b1-387">Farklı bir dosya uzantısını kullanmak için `.bin` `.bin` Yukarıdaki komutlarda değiştirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-387">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="331b1-388">Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları çözmek için `blazor.boot.json.br` aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-388">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="331b1-389">Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları kaldırın `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="331b1-389">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="331b1-390">Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="331b1-390">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="331b1-391">Güncelleştirilmiş dosyayı yeniden sıkıştırın `blazor.boot.json` .</span><span class="sxs-lookup"><span data-stu-id="331b1-391">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="331b1-392">Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="331b1-392">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="331b1-393">Ve ' i kaldırın veya yeniden sıkıştırın `wwwroot/service-worker-assets.js.br` `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="331b1-393">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="331b1-394">Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="331b1-394">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="331b1-395">Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-395">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="331b1-396">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="331b1-396">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="331b1-397">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="331b1-397">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="331b1-398">Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="331b1-398">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="331b1-399">Aynı derlemeleri yeniden adlandırırken ve geç yüklerken, içindeki kılavuza bakın <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="331b1-399">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="331b1-400">Bütünlük denetimi başarısızlıklarını çözümleyin</span><span class="sxs-lookup"><span data-stu-id="331b1-400">Resolve integrity check failures</span></span>

<span data-ttu-id="331b1-401">Blazor WebAssemblyUygulamanın başlangıç dosyalarını indirdiğinde, tarayıcıya yanıtlar üzerinde bütünlük denetimleri gerçekleştirmesini söyler.</span><span class="sxs-lookup"><span data-stu-id="331b1-401">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="331b1-402">`blazor.boot.json` `.dll` , Ve diğer dosyalar IÇIN beklenen SHA-256 karma değerlerini belirtmek için dosyadaki bilgileri kullanır `.wasm` .</span><span class="sxs-lookup"><span data-stu-id="331b1-402">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="331b1-403">Bu, aşağıdaki nedenlerle faydalıdır:</span><span class="sxs-lookup"><span data-stu-id="331b1-403">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="331b1-404">Örneğin, Kullanıcı uygulama dosyalarını indirme sürecinde olduğunda, Web sunucunuza yeni bir dağıtım uygulanmışsa, tutarsız bir dosya kümesini yükleme riskini almanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="331b1-404">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="331b1-405">Tutarsız dosyalar tanımsız davranışa yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-405">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="331b1-406">Kullanıcının tarayıcısının hiçbir şekilde tutarsız veya geçersiz yanıtları önbelleklemesini sağlar, bu da sayfayı el ile yenilese bile uygulamayı başlatmalarını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-406">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="331b1-407">Yanıtları önbelleğe alma işlemini güvenli hale getirir ve beklenen SHA-256 karmaları değişene kadar sunucu tarafı değişikliklerini kontrol etmez, sonraki sayfa yüklemeleri daha az istek içerir ve çok daha hızlı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-407">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="331b1-408">Web sunucunuz beklenen SHA-256 karmalarıyla eşleşmeyen yanıtlar döndürürse, tarayıcının geliştirici konsolunda aşağıdakine benzer bir hata görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="331b1-408">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="331b1-409">Çoğu durumda bu, bütünlük denetimi ile ilgili bir sorun *değildir* .</span><span class="sxs-lookup"><span data-stu-id="331b1-409">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="331b1-410">Bunun yerine, başka bir sorun olduğu anlamına gelir ve bu sorunu gidermek için bütünlük denetimi size uyarı verebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-410">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="331b1-411">Bütünlük sorunlarını tanılama</span><span class="sxs-lookup"><span data-stu-id="331b1-411">Diagnosing integrity problems</span></span>

<span data-ttu-id="331b1-412">Bir uygulama oluşturulduğunda, oluşturulan `blazor.boot.json` bildirim, derleme çıktısının oluşturulduğu sırada önyükleme KAYNAKLARıNıZıN SHA-256 karmalarını (örneğin,, `.dll` `.wasm` ve diğer dosyaları) açıklar.</span><span class="sxs-lookup"><span data-stu-id="331b1-412">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="331b1-413">' Deki SHA-256 karmaları `blazor.boot.json` tarayıcıya teslim edilen dosyalarla eşleştiği sürece bütünlük denetimi geçirilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-413">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="331b1-414">Bunun başarısız olmasının yaygın nedenleri:</span><span class="sxs-lookup"><span data-stu-id="331b1-414">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="331b1-415">Web sunucusunun yanıtı, tarayıcı istenen dosya yerine bir hatadır (örneğin, bir *404-bulunamadı* veya *500-Internal Server Error*).</span><span class="sxs-lookup"><span data-stu-id="331b1-415">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="331b1-416">Bu, tarayıcı tarafından bir bütünlük denetimi hatası olarak bildirilir ve yanıt hatası olarak değildir.</span><span class="sxs-lookup"><span data-stu-id="331b1-416">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="331b1-417">Dosya içeriğini tarayıcıya, dosyaların oluşturulması ve teslimi arasında değiştiren bir sorun.</span><span class="sxs-lookup"><span data-stu-id="331b1-417">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="331b1-418">Bu durum oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="331b1-418">This might happen:</span></span>
   * <span data-ttu-id="331b1-419">Ya da yapı araçları derleme çıkışını el ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="331b1-419">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="331b1-420">Dağıtım işleminin bazı bir yönü dosyaları değiştirdiyseniz.</span><span class="sxs-lookup"><span data-stu-id="331b1-420">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="331b1-421">Örneğin, git tabanlı bir dağıtım mekanizması kullanıyorsanız, Windows üzerinde dosya oluşturup Linux 'ta kullanıma alırsanız git 'in Windows stili satır sonlarını şeffaf olarak UNIX stili satır sonlarına dönüştürdüğünü göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="331b1-421">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="331b1-422">Dosya satır sonlarını değiştirmek için SHA-256 karmaları değiştirin.</span><span class="sxs-lookup"><span data-stu-id="331b1-422">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="331b1-423">Bu sorundan kaçınmak için, [ `.gitattributes` Yapı yapıtlarını `binary` dosya olarak değerlendirmek üzere](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="331b1-423">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="331b1-424">Web sunucusu, bu dosyaları sunma bölümünde dosya içeriğini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="331b1-424">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="331b1-425">Örneğin, bazı içerik dağıtım ağları (CDNs) otomatik olarak HTML 'yi [küçültmeye](xref:client-side/bundling-and-minification#minification) çalışır ve bu sayede onu değiştirir.</span><span class="sxs-lookup"><span data-stu-id="331b1-425">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="331b1-426">Bu tür özellikleri devre dışı bırakmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-426">You may need to disable such features.</span></span>

<span data-ttu-id="331b1-427">Bu durumun hangi durumlarda geçerli olduğunu tanılamak için:</span><span class="sxs-lookup"><span data-stu-id="331b1-427">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="331b1-428">Hatanın hata iletisini okuyarak tetikleneceği dosyayı aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="331b1-428">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="331b1-429">Tarayıcınızın geliştirici araçlarını açın ve *ağ* sekmesine bakın. Gerekirse, istek ve yanıtların listesini görmek için sayfayı yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="331b1-429">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="331b1-430">Bu listedeki hatayı tetikleyen dosyayı bulun.</span><span class="sxs-lookup"><span data-stu-id="331b1-430">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="331b1-431">Yanıttaki HTTP durum kodunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="331b1-431">Check the HTTP status code in the response.</span></span> <span data-ttu-id="331b1-432">Sunucu *200-Tamam* (veya başka bir 2xx durum kodu) dışında bir şey döndürürse, tanılamaya yönelik bir sunucu tarafı sorununuz vardır.</span><span class="sxs-lookup"><span data-stu-id="331b1-432">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="331b1-433">Örneğin, 403 durum kodu bir yetkilendirme sorunu olduğu anlamına gelirken durum kodu 500, sunucunun belirtilmeyen bir şekilde başarısız olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="331b1-433">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="331b1-434">Uygulamayı tanılamak ve onarmak için sunucu tarafı günlüklerine başvurun.</span><span class="sxs-lookup"><span data-stu-id="331b1-434">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="331b1-435">Durum kodu kaynak için *200-Tamam* ise, tarayıcının geliştirici araçlarındaki yanıt içeriğine bakın ve içeriğin beklenen verilerle eşleşip eşleşmediğini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="331b1-435">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matches up with the data expected.</span></span> <span data-ttu-id="331b1-436">Örneğin, yaygın bir sorun, isteklerin `index.html` diğer dosyalar için bile verilerinizi döndürmesi için yönlendirmeyi yanlış yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="331b1-436">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="331b1-437">`.wasm`İsteklerin yanıtlarının, Weelsembly ikilileriyle ve `.dll` isteklerin yanıtlarının .NET Derleme ikili dosyalarına sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="331b1-437">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="331b1-438">Aksi takdirde, tanılamaya yönelik bir sunucu tarafı yönlendirme sorununuz vardır.</span><span class="sxs-lookup"><span data-stu-id="331b1-438">If not, you have a server-side routing problem to diagnose.</span></span>
 1. <span data-ttu-id="331b1-439">[Sorun giderme bütünlüğü PowerShell betiği](#troubleshoot-integrity-powershell-script)ile uygulamanın yayımlanmış ve dağıtılan çıkışını doğrulamak için arama yapın.</span><span class="sxs-lookup"><span data-stu-id="331b1-439">Seek to validate the app's published and deployed output with the [Troubleshoot integrity PowerShell script](#troubleshoot-integrity-powershell-script).</span></span>

<span data-ttu-id="331b1-440">Sunucunun sürekli doğru verileri döndürdüğünü onaylamak için, dosyanın oluşturulması ve teslimi arasındaki içerikleri değiştirmek için başka bir şey olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="331b1-440">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="331b1-441">Bunu araştırmak için:</span><span class="sxs-lookup"><span data-stu-id="331b1-441">To investigate this:</span></span>

 * <span data-ttu-id="331b1-442">Dosyalar derlendikten sonra dosyaları değiştirirken derleme araç zinciri ve dağıtım mekanizmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="331b1-442">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="331b1-443">Git 'in, daha önce açıklandığı gibi dosya satır sonlarını dönüştürerken buna bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="331b1-443">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="331b1-444">Yanıtları dinamik olarak değiştirmek üzere ayarlanmış olmaları durumunda Web sunucusunu veya CDN yapılandırmasını inceleyin (örneğin, HTML 'yi küçültmeye çalışırken).</span><span class="sxs-lookup"><span data-stu-id="331b1-444">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="331b1-445">Web sunucusunun HTTP sıkıştırması uygulaması (örneğin, döndürme `content-encoding: br` veya döndürme `content-encoding: gzip` ), bu nedenle bu, sıkıştırmayı açma işleminden sonra sonucu etkilemez.</span><span class="sxs-lookup"><span data-stu-id="331b1-445">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="331b1-446">Ancak, Web sunucusunun sıkıştırılmamış verileri değiştirmesi iyi *değildir* .</span><span class="sxs-lookup"><span data-stu-id="331b1-446">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="troubleshoot-integrity-powershell-script"></a><span data-ttu-id="331b1-447">Bütünlük PowerShell betiği sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="331b1-447">Troubleshoot integrity PowerShell script</span></span>

<span data-ttu-id="331b1-448">[`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true)Yayımlanmış ve dağıtılan bir uygulamayı doğrulamak için PowerShell betiğini kullanın Blazor .</span><span class="sxs-lookup"><span data-stu-id="331b1-448">Use the [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell script to validate a published and deployed Blazor app.</span></span> <span data-ttu-id="331b1-449">Komut dosyası, uygulamanın, Blazor Framework 'ün tanımlayamıyorum bütünlük sorunları olduğunda bir başlangıç noktası olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="331b1-449">The script is provided as a starting point when the app has integrity issues that the Blazor framework can't identify.</span></span> <span data-ttu-id="331b1-450">Uygulamanız için betiğin özelleştirilmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-450">Customization of the script might be required for your apps.</span></span>

<span data-ttu-id="331b1-451">Betik, klasördeki dosyaları denetler `publish` ve bütünlük karmalarını içeren farklı bildirimlerde sorunları algılamak için dağıtılan uygulamadan indirilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-451">The script checks the files in the `publish` folder and downloaded from the deployed app to detect issues in the different manifests that contain integrity hashes.</span></span> <span data-ttu-id="331b1-452">Bu denetimler en yaygın sorunları algılamamalıdır:</span><span class="sxs-lookup"><span data-stu-id="331b1-452">These checks should detect the most common problems:</span></span>

* <span data-ttu-id="331b1-453">Yayımlanan çıktıda bir dosyayı gerçekleştirmeden değiştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="331b1-453">You modified a file in the published output without realizing it.</span></span>
* <span data-ttu-id="331b1-454">Uygulama, dağıtım hedefine doğru şekilde dağıtılmadı veya dağıtım hedefinin ortamında bir değişiklik yapılamayabilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-454">The app wasn't correctly deployed to the deployment target, or something changed within the deployment target's environment.</span></span>
* <span data-ttu-id="331b1-455">Dağıtılan uygulamayla çıkış arasında uygulamanın yayımlaması arasında farklılıklar vardır.</span><span class="sxs-lookup"><span data-stu-id="331b1-455">There are differences between the deployed app and the output from publishing the app.</span></span>

<span data-ttu-id="331b1-456">PowerShell komut kabuğu 'nda aşağıdaki komutla betiği çağırın:</span><span class="sxs-lookup"><span data-stu-id="331b1-456">Invoke the script with the following command in a PowerShell command shell:</span></span>

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

<span data-ttu-id="331b1-457">İçeremez</span><span class="sxs-lookup"><span data-stu-id="331b1-457">Placeholders:</span></span>

* <span data-ttu-id="331b1-458">`{BASE URL}`: Dağıtılan uygulamanın URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="331b1-458">`{BASE URL}`: The URL of the deployed app.</span></span>
* <span data-ttu-id="331b1-459">`{PUBLISH OUTPUT FOLDER}`: Uygulamanın `publish` dağıtım için yayımlandığı klasörün veya konumun yolu.</span><span class="sxs-lookup"><span data-stu-id="331b1-459">`{PUBLISH OUTPUT FOLDER}`: The path to the app's `publish` folder or location where the app is published for deployment.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="331b1-460">PWA olmayan uygulamalar için bütünlük denetimini devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="331b1-460">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="331b1-461">Çoğu durumda, bütünlük denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="331b1-461">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="331b1-462">Bütünlük denetimini devre dışı bırakmak, beklenmeyen yanıtlara neden olan temeldeki sorunu çözmez ve daha önce listelenen avantajları kaybetme sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="331b1-462">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="331b1-463">Web sunucusunun tutarlı yanıtlar döndürecek şekilde güvenmeme, ancak hiçbir seçim olmadığı ancak bütünlük denetimlerini devre dışı bırakabileceği durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="331b1-463">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="331b1-464">Bütünlük denetimlerini devre dışı bırakmak için aşağıdakini proje dosyasındaki bir özellik grubuna ekleyin Blazor WebAssembly `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="331b1-464">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="331b1-465">`BlazorCacheBootResources`Blazor `.dll` , `.wasm` özelliği SHA-256 karmalarını temel alarak,, ve diğer dosyaları önbelleğe almanın varsayılan davranışını devre dışı bırakır çünkü özellik SHA-256 karmalarını doğruluk açısından güvenemediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="331b1-465">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="331b1-466">Bu ayar ile birlikte, tarayıcının normal HTTP önbelleği bu dosyaları önbelleğe almaya devam edebilir, ancak bunun gerçekleşmediğine bakılmaksızın Web sunucusu yapılandırmanıza ve `cache-control` hizmet verdiği üstbilgilere göre değişir.</span><span class="sxs-lookup"><span data-stu-id="331b1-466">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="331b1-467">`BlazorCacheBootResources`Özelliği, [aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)için bütünlük denetimlerini devre dışı bırakmıyor.</span><span class="sxs-lookup"><span data-stu-id="331b1-467">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="331b1-468">PWAs ile ilgili rehberlik için [PWAs için bütünlük denetimini devre dışı bırakma](#disable-integrity-checking-for-pwas) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="331b1-468">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="331b1-469">PWAs için bütünlük denetimini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="331b1-469">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="331b1-470">BlazorAşamalı Web uygulaması (PWA) şablonu, `service-worker.published.js` çevrimdışı kullanım için uygulama dosyalarını getirmekten ve saklamaktan sorumlu önerilen bir dosya içerir.</span><span class="sxs-lookup"><span data-stu-id="331b1-470">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="331b1-471">Bu, normal uygulama başlangıç mekanizmasından ayrı bir işlemdir ve kendi ayrı bütünlük denetimi mantığına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="331b1-471">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="331b1-472">Dosya içinde `service-worker.published.js` Aşağıdaki satır mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="331b1-472">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="331b1-473">Bütünlük denetimini devre dışı bırakmak için, `integrity` satırı aşağıdaki şekilde değiştirerek parametreyi kaldırın:</span><span class="sxs-lookup"><span data-stu-id="331b1-473">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="331b1-474">Daha sonra, bütünlük denetimini devre dışı bırakmak, bütünlük denetimi tarafından sunulan güvenlik garantisi kaybetmeniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="331b1-474">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="331b1-475">Örneğin, kullanıcının tarayıcısı uygulamayı yeni bir sürümünü dağıttığınız andan itibaren önbelleğe alıyorsa, eski dağıtımdan ve bazıları yeni dağıtımdan bazı dosyaları önbelleğe alabilir, bu da bir risk vardır.</span><span class="sxs-lookup"><span data-stu-id="331b1-475">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="331b1-476">Bu durumda, daha fazla güncelleştirme dağıtana kadar uygulama bozuk bir durumda takılmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="331b1-476">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
