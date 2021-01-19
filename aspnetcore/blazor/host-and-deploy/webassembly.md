---
title: ASP.NET Core barındırma ve dağıtma Blazor WebAssembly
author: guardrex
description: BlazorASP.NET Core, Içerik teslim ağları (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
ms.openlocfilehash: 2b464c2b6ca434ce4c3b559480da69945266ff69
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570979"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="2d8cb-103">ASP.NET Core barındırma ve dağıtma Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2d8cb-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="2d8cb-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından</span><span class="sxs-lookup"><span data-stu-id="2d8cb-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="2d8cb-105">[ Blazor WebAssembly Barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="2d8cb-106">BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="2d8cb-107">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="2d8cb-108">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="2d8cb-109">BlazorUygulama, bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="2d8cb-110">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="2d8cb-111">BlazorUygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz Blazor .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="2d8cb-112">Bu strateji, bir uygulamayı IIS alt uygulaması olarak barındırma hakkında bilgi içeren [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="2d8cb-113">Sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-113">Compression</span></span>

<span data-ttu-id="2d8cb-114">Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="2d8cb-115">Aşağıdaki sıkıştırma algoritmaları kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="2d8cb-116">[Brotli](https://tools.ietf.org/html/rfc7932) (en yüksek düzey)</span><span class="sxs-lookup"><span data-stu-id="2d8cb-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="2d8cb-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="2d8cb-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="2d8cb-118">Blazor , uygun sıkıştırılmış dosyaları sunacak ana bilgisayarı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="2d8cb-119">ASP.NET Core barındırılan bir proje kullanırken, konak proje içerik anlaşması yapabilir ve statik olarak sıkıştırılmış dosyalara hizmet verebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="2d8cb-120">Blazor WebAssemblyTek başına bir uygulamayı barındırırken, statik olarak sıkıştırılmış dosyaların sunulmasını sağlamak için ek çalışma gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="2d8cb-121">IIS `web.config` sıkıştırma yapılandırması Için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="2d8cb-122">GitHub sayfaları gibi statik olarak sıkıştırılmış dosya içeriği anlaşmasını desteklemeyen statik barındırma çözümlerinde barındırırken, Brotli sıkıştırılan dosyaları getirmek ve kodunu çözmek üzere uygulamayı yapılandırmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="2d8cb-123">[Google/Brotli GitHub deposundan](https://github.com/google/brotli)JavaScript Brotli kod çözücüsünü edinin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="2d8cb-124">Kod çözücü dosyasının adı `decode.min.js` ve deponun [ `js` klasöründe](https://github.com/google/brotli/tree/master/js)bulunur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-124">The decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>

  * <span data-ttu-id="2d8cb-125">Kod çözücüyü kullanmak için uygulamayı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-125">Update the app to use the decoder.</span></span> <span data-ttu-id="2d8cb-126">İçindeki kapanış etiketinin içindeki biçimlendirmeyi `<body>` aşağıdaki gibi değiştirin `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-126">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="2d8cb-127">Sıkıştırmayı devre dışı bırakmak için `BlazorEnableCompression` uygulamanın proje dosyasına MSBuild özelliğini ekleyin ve değeri şu şekilde ayarlayın `false` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="2d8cb-128">`BlazorEnableCompression`Özelliği [`dotnet publish`](/dotnet/core/tools/dotnet-publish) komut kabuğunda aşağıdaki söz dizimi ile komuta geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-128">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="2d8cb-129">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="2d8cb-129">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="2d8cb-130">Bir uygulamadaki sayfa bileşenlerine yönelik yönlendirme istekleri Blazor WebAssembly , barındırılan bir uygulamadaki yönlendirme istekleri kadar basittir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-130">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="2d8cb-131">Blazor WebAssemblyİki bileşeni olan bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-131">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="2d8cb-132">`Main.razor`: Uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-132">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="2d8cb-133">`About.razor`: `About` bileşen.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-133">`About.razor`: `About` component.</span></span>

<span data-ttu-id="2d8cb-134">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-134">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="2d8cb-135">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-135">The browser makes a request.</span></span>
1. <span data-ttu-id="2d8cb-136">Varsayılan sayfa döndürülür, genellikle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-136">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="2d8cb-137">`index.html` uygulamayı önyükleme.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-137">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="2d8cb-138">Blazoruygulamasının yönlendirici yüklenir ve Razor `Main` bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-138">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="2d8cb-139">Ana sayfada, `About` Blazor yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-139">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="2d8cb-140">*Blazor WebAssembly Uygulamadaki* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-140">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="2d8cb-141">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-141">The router handles the requests internally.</span></span>

<span data-ttu-id="2d8cb-142">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-142">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="2d8cb-143">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-143">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="2d8cb-144">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri sunucuda fiziksel olarak olmayan tüm kaynak isteklerini sayfaya yeniden yazması gerekir `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-144">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="2d8cb-145">`index.html`Döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-145">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="2d8cb-146">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayımlanan dosyasıyla birlikte kullanabilirsiniz `web.config` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-146">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="2d8cb-147">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-147">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="2d8cb-148">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="2d8cb-148">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="2d8cb-149">*Barındırılan bir dağıtım* , Blazor WebAssembly uygulamayı bir Web sunucusu üzerinde çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılarına sunar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-149">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="2d8cb-150">İstemci uygulaması, sunucu uygulamasının Blazor WebAssembly `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` diğer statik Web varlıklarıyla birlikte sunucu uygulamasının klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-150">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="2d8cb-151">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-151">The two apps are deployed together.</span></span> <span data-ttu-id="2d8cb-152">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-152">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="2d8cb-153">Barındırılan bir dağıtım için, Visual Studio **Blazor WebAssembly uygulama** proje şablonunu (komutunu kullanırken `blazorwasm` şablon [`dotnet new`](/dotnet/core/tools/dotnet-new) ), **`Hosted`** seçeneği belirlendiğinde ( `-ho|--hosted` `dotnet new` komutunu kullanırken) içerir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-153">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="2d8cb-154">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="2d8cb-154">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2d8cb-155">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="2d8cb-155">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="2d8cb-156">Birden çok uygulama ile barındırılan dağıtım Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2d8cb-156">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="2d8cb-157">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2d8cb-157">App configuration</span></span>

<span data-ttu-id="2d8cb-158">Barındırılan Blazor çözümler birden çok Blazor WebAssembly uygulamayı kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-158">Hosted Blazor solutions can serve multiple Blazor WebAssembly apps.</span></span>

> [!NOTE]
> <span data-ttu-id="2d8cb-159">Bu bölümdeki örnek, bir Visual Studio *çözümünün* kullanımına başvurur, ancak birden fazla istemci uygulamasının barındırılan uygulamalar senaryosunda çalışması Için Visual Studio ve Visual Studio çözümünün kullanımı gerekli değildir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-159">The example in this section references the use of a Visual Studio *solution*, but the use of Visual Studio and a Visual Studio solution isn't required for multiple client apps to work in a hosted Blazor WebAssembly apps scenario.</span></span> <span data-ttu-id="2d8cb-160">Visual Studio kullanmıyorsanız, `{SOLUTION NAME}.sln` dosyayı ve Visual Studio için oluşturulan diğer dosyaları yoksayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-160">If you aren't using Visual Studio, ignore the `{SOLUTION NAME}.sln` file and any other files created for Visual Studio.</span></span>

<span data-ttu-id="2d8cb-161">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-161">In the following example:</span></span>

* <span data-ttu-id="2d8cb-162">İlk (ilk) istemci uygulaması, proje şablonundan oluşturulan bir çözümün varsayılan istemci projem Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-162">The initial (first) client app is the default client project of a solution created from the Blazor WebAssembly project template.</span></span> <span data-ttu-id="2d8cb-163">İlk istemci uygulamasına, `/FirstApp` bağlantı noktası 5001 ya da bir KONAĞıNDAN URL 'den bir tarayıcıda erişilebilir `firstapp.com` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-163">The first client app is accessible in a browser from the URL `/FirstApp` on either port 5001 or with a host of `firstapp.com`.</span></span>
* <span data-ttu-id="2d8cb-164">Çözüme ikinci bir istemci uygulaması eklenir `SecondBlazorApp.Client` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-164">A second client app is added to the solution, `SecondBlazorApp.Client`.</span></span> <span data-ttu-id="2d8cb-165">İkinci istemci uygulamasına, `/SecondApp` bağlantı noktası 5002 ya da bir KONAĞıNDAN URL 'den bir tarayıcıda erişilebilir `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-165">The second client app is accessible in a browser from the the URL `/SecondApp` on either port 5002 or with a host of `secondapp.com`.</span></span>

<span data-ttu-id="2d8cb-166">BlazorBarındırılan proje şablonundan mevcut bir barındırılan çözümü kullanın veya yeni bir çözüm oluşturun Blazor :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-166">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template:</span></span>

* <span data-ttu-id="2d8cb-167">İstemci uygulamasının proje dosyasında, `<StaticWebAssetBasePath>` `<PropertyGroup>` `FirstApp` projenin statik varlıkların temel yolunu ayarlamak için değeri ile öğesine bir özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-167">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="2d8cb-168">Çözüme ikinci bir istemci uygulaması ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-168">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="2d8cb-169">Çözümün klasörüne adlı bir klasör ekleyin `SecondClient` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-169">Add a folder named `SecondClient` to the solution's folder.</span></span> <span data-ttu-id="2d8cb-170">Proje şablonundan oluşturulan çözüm klasörü, klasör eklendikten sonra aşağıdaki çözüm dosya ve klasörlerini içerir `SecondClient` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-170">The solution folder created from the project template contains the following solution file and folders after the `SecondClient` folder is added:</span></span>
  
    * <span data-ttu-id="2d8cb-171">`Client` klasörde</span><span class="sxs-lookup"><span data-stu-id="2d8cb-171">`Client` (folder)</span></span>
    * <span data-ttu-id="2d8cb-172">`SecondClient` klasörde</span><span class="sxs-lookup"><span data-stu-id="2d8cb-172">`SecondClient` (folder)</span></span>
    * <span data-ttu-id="2d8cb-173">`Server` klasörde</span><span class="sxs-lookup"><span data-stu-id="2d8cb-173">`Server` (folder)</span></span>
    * <span data-ttu-id="2d8cb-174">`Shared` klasörde</span><span class="sxs-lookup"><span data-stu-id="2d8cb-174">`Shared` (folder)</span></span>
    * <span data-ttu-id="2d8cb-175">`{SOLUTION NAME}.sln` dosyasýný</span><span class="sxs-lookup"><span data-stu-id="2d8cb-175">`{SOLUTION NAME}.sln` (file)</span></span>
    
    <span data-ttu-id="2d8cb-176">Yer tutucu `{SOLUTION NAME}` çözümün adıdır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-176">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

  * <span data-ttu-id="2d8cb-177">Blazor WebAssembly `SecondBlazorApp.Client` `SecondClient` Proje şablonundan klasöründe adlı bir uygulama oluşturun Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-177">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>

  * <span data-ttu-id="2d8cb-178">`SecondBlazorApp.Client`Uygulamanın proje dosyasında:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-178">In the `SecondBlazorApp.Client` app's project file:</span></span>

    * <span data-ttu-id="2d8cb-179">`<StaticWebAssetBasePath>`Değerine sahip öğesine bir özelliği ekleyin `<PropertyGroup>` `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-179">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="2d8cb-180">Projeye bir proje başvurusu ekleyin `Shared` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-180">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="2d8cb-181">Yer tutucu `{SOLUTION NAME}` çözümün adıdır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-181">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="2d8cb-182">Sunucu uygulamasının proje dosyasında, eklenen istemci uygulaması için bir proje başvurusu oluşturun `SecondBlazorApp.Client` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-182">In the server app's project file, create a project reference for the added `SecondBlazorApp.Client` client app:</span></span>

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  <span data-ttu-id="2d8cb-183">Yer tutucu `{SOLUTION NAME}` çözümün adıdır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-183">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="2d8cb-184">Sunucu uygulamasının `Properties/launchSettings.json` dosyasında, `applicationUrl` `{SOLUTION NAME}.Server` 5001 ve 5002 bağlantı noktalarında istemci uygulamalarına erişmek için Kestrel profili () öğesini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-184">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="2d8cb-185">Sunucu uygulamasının `Startup.Configure` yönteminde ( `Startup.cs` ), çağrısından sonra görünen aşağıdaki satırları kaldırın <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-185">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="2d8cb-186">İstekleri istemci uygulamalarına eşleyen ara yazılım ekleme.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-186">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="2d8cb-187">Aşağıdaki örnek, şu durumlarda ara yazılımı çalıştıracak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-187">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="2d8cb-188">İstek bağlantı noktası, özgün istemci uygulaması için 5001 ya da eklenen istemci uygulaması için 5002 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-188">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="2d8cb-189">İstek Konağı, `firstapp.com` özgün istemci uygulaması ya da `secondapp.com` eklenen istemci uygulaması için.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-189">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="2d8cb-190">Bu bölümde gösterilen örnekte, için ek yapılandırma gerekir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-190">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="2d8cb-191">Örnek ana bilgisayar etki alanlarındaki uygulamalara erişme `firstapp.com` ve `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-191">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="2d8cb-192">TLS güvenliğini etkinleştirmek için istemci uygulamalarına yönelik Sertifikalar (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-192">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="2d8cb-193">Gerekli yapılandırma Bu makalenin kapsamı dışındadır ve çözümün nasıl barındırıldığını bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-193">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="2d8cb-194">Daha fazla bilgi için bkz. [konak ve dağıtım makaleleri](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-194">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="2d8cb-195">Aşağıdaki kodu satırların daha önce kaldırıldığı yere yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-195">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="2d8cb-196">Sunucu uygulamasının Hava durumu tahmin denetleyicisi 'nde ( `Controllers/WeatherForecastController.cs` ), var olan yolu ( `[Route("[controller]")]` ) `WeatherForecastController` aşağıdaki yollarla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-196">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="2d8cb-197">Sunucu uygulamasının yöntemine eklenen ara yazılım, `Startup.Configure` gelen istekleri `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` bağlantı noktasına (5001/5002) veya etki alanına () bağlı olarak ya da öğesine göre değiştirir `firstapp.com` / `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-197">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="2d8cb-198">Sunucu uygulamasından istemci uygulamalarına Hava durumu verilerini döndürmek için önceki denetleyici yolları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-198">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="2d8cb-199">Statik varlıklar ve sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="2d8cb-199">Static assets and class libraries</span></span>

<span data-ttu-id="2d8cb-200">Statik varlıklar için aşağıdaki yaklaşımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-200">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="2d8cb-201">Varlık, istemci uygulamasının `wwwroot` klasöründe olduğunda, kendi yollarını normal olarak sağlayın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-201">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="2d8cb-202">Varlık `wwwroot` bir [ Razor sınıf kitaplığı (RCL)](xref:blazor/components/class-libraries)klasöründe olduğunda, [RCL makalesindeki](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)kılavuzluk uyarınca istemci uygulamasındaki statik varlığa başvurun:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-202">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

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

<span data-ttu-id="2d8cb-203">Bir sınıf kitaplığı tarafından bir istemci uygulamasına verilen bileşenlere normal olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-203">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="2d8cb-204">Herhangi bir bileşen stil sayfaları veya JavaScript dosyaları gerektiriyorsa, istemci uygulamanın `wwwroot/index.html` dosyası doğru statik varlık bağlantılarını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-204">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="2d8cb-205">Bu yaklaşımlar aşağıdaki örneklerde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-205">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="2d8cb-206">Aşağıdaki `Jeep` bileşeni istemci uygulamalarından birine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-206">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="2d8cb-207">`Jeep`Bileşen şunları kullanır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-207">The `Jeep` component uses:</span></span>

* <span data-ttu-id="2d8cb-208">İstemci uygulamanın `wwwroot` klasöründen () bir resim `jeep-cj.png` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-208">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="2d8cb-209">[Eklenen Razor Bileşen kitaplığı](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` klasöründen () bir resim `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-209">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="2d8cb-210">Örnek bileşen ( `Component1` ), kitaplık çözüme eklendiğinde RCL proje şablonu tarafından otomatik olarak oluşturulur `JeepImage` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-210">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="2d8cb-211">Görüntülerin sahibi olmadığınız takdirde, taşıtlar görüntülerini **herkese yayımlamayın** .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-211">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="2d8cb-212">Aksi takdirde, telif hakkı ihlali riski vardır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-212">Otherwise, you risk copyright infringement.</span></span>

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

<span data-ttu-id="2d8cb-213">Kitaplığın `jeep-yj.png` görüntüsü kitaplığın bileşenine de eklenebilir `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-213">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="2d8cb-214">İstemci uygulamasının dosyası, `wwwroot/index.html` aşağıdaki eklenmiş etikete sahip kitaplığın stil sayfasını ister `<link>` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-214">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="2d8cb-215">`Jeep`İstemci uygulamasının bileşeninde bileşene gezinti ekleyin `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-215">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="2d8cb-216">RCLs hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-216">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="2d8cb-217">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="2d8cb-217">Standalone deployment</span></span>

<span data-ttu-id="2d8cb-218">*Tek başına dağıtım* , Blazor WebAssembly uygulamaya doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-218">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="2d8cb-219">Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-219">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="2d8cb-220">Tek başına dağıtım varlıkları `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-220">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="2d8cb-221">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2d8cb-221">Azure App Service</span></span>

<span data-ttu-id="2d8cb-222">Blazor WebAssembly uygulamalar, uygulamayı [IIS](#iis)'de barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-222">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="2d8cb-223">Tek başına bir Blazor WebAssembly uygulamanın Linux için Azure App Service dağıtımı şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-223">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="2d8cb-224">Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-224">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="2d8cb-225">Bu senaryoyu etkinleştirmek için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-225">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="2d8cb-226">IIS</span><span class="sxs-lookup"><span data-stu-id="2d8cb-226">IIS</span></span>

<span data-ttu-id="2d8cb-227">IIS, uygulamalar için özellikli bir statik dosya sunucusudur Blazor .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-227">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="2d8cb-228">IIS 'yi barındıracak şekilde yapılandırmak için Blazor bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-228">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="2d8cb-229">Yayımlanan varlıklar `/bin/Release/{TARGET FRAMEWORK}/publish` klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-229">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="2d8cb-230">`publish`Web sunucusunda veya barındırma hizmetinde klasörün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-230">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="2d8cb-231">web.config</span><span class="sxs-lookup"><span data-stu-id="2d8cb-231">web.config</span></span>

<span data-ttu-id="2d8cb-232">Bir Blazor Proje yayımlandığında, `web.config` aşağıdaki IIS yapılandırmasıyla bir dosya oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-232">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="2d8cb-233">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-233">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="2d8cb-234">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="2d8cb-234">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="2d8cb-235">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="2d8cb-235">`.json`: `application/json`</span></span>
  * <span data-ttu-id="2d8cb-236">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="2d8cb-236">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="2d8cb-237">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2d8cb-237">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="2d8cb-238">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2d8cb-238">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="2d8cb-239">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-239">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="2d8cb-240">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-240">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="2d8cb-241">Uygulamanın statik varlıklarının bulunduğu alt dizini ( `wwwroot/{PATH REQUESTED}` ) sunar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-241">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="2d8cb-242">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe () uygulamanın varsayılan belgesine yeniden yönlendirilmesi için SPA geri dönüş yönlendirmesi oluşturun `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-242">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="2d8cb-243">Özel bir web.config kullanma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-243">Use a custom web.config</span></span>

<span data-ttu-id="2d8cb-244">Özel bir dosya kullanmak için `web.config` , özel `web.config` dosyayı proje klasörünün köküne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-244">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="2d8cb-245">Projeyi uygulamanın proje dosyasında kullanarak IIS 'e özgü varlıkları yayımlayacak şekilde yapılandırın `PublishIISAssets` ve projeyi yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-245">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="2d8cb-246">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="2d8cb-246">Install the URL Rewrite Module</span></span>

<span data-ttu-id="2d8cb-247">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-247">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="2d8cb-248">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-248">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="2d8cb-249">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-249">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="2d8cb-250">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-250">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="2d8cb-251">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-251">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="2d8cb-252">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-252">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="2d8cb-253">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-253">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="2d8cb-254">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-254">Copy the installer to the server.</span></span> <span data-ttu-id="2d8cb-255">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-255">Run the installer.</span></span> <span data-ttu-id="2d8cb-256">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-256">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="2d8cb-257">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-257">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="2d8cb-258">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-258">Configure the website</span></span>

<span data-ttu-id="2d8cb-259">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-259">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="2d8cb-260">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-260">The folder contains:</span></span>

* <span data-ttu-id="2d8cb-261">`web.config`Web sitesini yapılandırmak için gereken yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS tarafından kullanılan dosya.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-261">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="2d8cb-262">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-262">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="2d8cb-263">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-263">Host as an IIS sub-app</span></span>

<span data-ttu-id="2d8cb-264">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-264">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="2d8cb-265">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-265">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="2d8cb-266">Blazor `web.config` Dosyaya bir bölüm ekleyerek uygulamanın yayımlanmış dosyasındaki işleyiciyi kaldırın `<handlers>` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-266">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="2d8cb-267">Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-267">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="2d8cb-268">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:blazor/host-and-deploy/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-268">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="2d8cb-269">Uygulamanın dosyasındaki uygulama temel yolunu, `index.html` IIS 'de alt uygulamayı yapılandırırken kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-269">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="2d8cb-270">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="2d8cb-270">Brotli and Gzip compression</span></span>

<span data-ttu-id="2d8cb-271">*Bu bölüm yalnızca tek başına uygulamalar için geçerlidir Blazor WebAssembly . barındırılan Blazor uygulamalar `web.config` , bu bölümde bağlantılı dosyayı değil, varsayılan bir ASP.NET Core uygulama dosyası kullanır.*</span><span class="sxs-lookup"><span data-stu-id="2d8cb-271">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="2d8cb-272">IIS, `web.config` Blazor tek başına uygulamalar için Brotli veya gzip ile sıkıştırılmış varlıklar sunacak şekilde yapılandırılabilir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-272">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="2d8cb-273">Örnek bir yapılandırma dosyası için bkz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) ..</span><span class="sxs-lookup"><span data-stu-id="2d8cb-273">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="2d8cb-274">Örnek dosyanın ek yapılandırması `web.config` aşağıdaki senaryolarda gerekli olabilir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-274">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="2d8cb-275">Uygulamanın belirtimi aşağıdakilerden birini çağırır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-275">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="2d8cb-276">Örnek dosya tarafından yapılandırılmayan sıkıştırılmış dosyalara hizmet sunma `web.config` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-276">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="2d8cb-277">Sıkıştırılmış dosyalara, sıkıştırılmamış biçimde örnek dosya tarafından yapılandırılmış olarak sunma `web.config` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-277">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="2d8cb-278">Sunucunun IIS yapılandırması (örneğin, `applicationHost.config` ) sunucu DÜZEYINDE IIS Varsayılanları sağlar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-278">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="2d8cb-279">Sunucu düzeyindeki yapılandırmaya bağlı olarak, uygulama örnek dosyanın içerenden farklı bir IIS yapılandırması gerektirebilir `web.config` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-279">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="2d8cb-280">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="2d8cb-280">Troubleshooting</span></span>

<span data-ttu-id="2d8cb-281">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-281">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="2d8cb-282">Modül yüklü olmadığında, `web.config` Dosya IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-282">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="2d8cb-283">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller Blazor .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-283">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="2d8cb-284">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="2d8cb-284">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="2d8cb-285">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="2d8cb-285">Azure Storage</span></span>

<span data-ttu-id="2d8cb-286">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-286">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="2d8cb-287">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-287">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="2d8cb-288">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-288">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="2d8cb-289">**Dizin belgesi adını** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-289">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="2d8cb-290">**Hata belge yolunu** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-290">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="2d8cb-291">Razor bileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-291">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="2d8cb-292">Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında Blazor , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna** yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-292">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="2d8cb-293">`index.html`BLOB döndürülür ve Blazor yönlendirici yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-293">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="2d8cb-294">Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-294">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="2d8cb-295">Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-295">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="2d8cb-296">`Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-296">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="2d8cb-297">Her dosya için Depolama Gezgini (Azure portal):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-297">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="2d8cb-298">Dosyaya sağ tıklayın ve **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-298">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="2d8cb-299">**ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-299">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="2d8cb-300">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-300">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="2d8cb-301">Nginx</span><span class="sxs-lookup"><span data-stu-id="2d8cb-301">Nginx</span></span>

<span data-ttu-id="2d8cb-302">Aşağıdaki `nginx.conf` Dosya, NGINX 'in `index.html` diskteki karşılık gelen bir dosyayı bulamadığı her seferinde dosyayı göndermek üzere nasıl yapılandırılacağını göstermek için basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-302">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="2d8cb-303">[NGINX veri bloğu hızı sınırı](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) ile ayarlandığında [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , Blazor WebAssembly uygulamalar `burst` bir uygulama tarafından yapılan görece çok sayıda isteği karşılamak için büyük bir parametre değeri gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-303">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="2d8cb-304">Başlangıçta değeri en az 60 olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-304">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="2d8cb-305">Tarayıcı geliştirici araçları veya ağ trafiği Aracı, isteklerin *503 servis dışı* bir durum kodu aldığını gösteriyorsa değeri artırın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-305">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="2d8cb-306">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-306">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="apache"></a><span data-ttu-id="2d8cb-307">Apache</span><span class="sxs-lookup"><span data-stu-id="2d8cb-307">Apache</span></span>

<span data-ttu-id="2d8cb-308">Bir Blazor WebAssembly uygulamayı CentOS 7 veya sonraki bir sürüme dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-308">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="2d8cb-309">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-309">Create the Apache configuration file.</span></span> <span data-ttu-id="2d8cb-310">Aşağıdaki örnek, Basitleştirilmiş bir yapılandırma dosyasıdır ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-310">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="2d8cb-311">Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-311">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="2d8cb-312">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-312">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="2d8cb-313">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-313">Restart the Apache service.</span></span>

<span data-ttu-id="2d8cb-314">Daha fazla bilgi için bkz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) . ve.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-314">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="2d8cb-315">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="2d8cb-315">GitHub Pages</span></span>

<span data-ttu-id="2d8cb-316">URL yeniden işlemesini işlemek için, `wwwroot/404.html` isteği sayfaya yönlendirmeyi işleyen bir betiği olan bir dosya ekleyin `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-316">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="2d8cb-317">Bir örnek için bkz. [Stevesandersonms/ Blazor ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="2d8cb-317">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="2d8cb-318">[Canlı site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="2d8cb-318">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="2d8cb-319">Bir kuruluş sitesi yerine bir proje sitesi kullanırken, `<base>` içindeki etiketini güncelleştirin `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-319">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="2d8cb-320">`href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin, `/my-repository/` ) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-320">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="2d8cb-321">[Stevesandersonms/ Blazor ongithubpages GitHub deposunda](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), temel `href` [ `.github/workflows/main.yml` yapılandırma dosyası](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)tarafından yayımlama sırasında güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-321">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="2d8cb-322">[Stevesandersonms/ Blazor ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) , .net Foundation veya Microsoft tarafından sahip değil, korunmuyor veya desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-322">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="2d8cb-323">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="2d8cb-323">Host configuration values</span></span>

<span data-ttu-id="2d8cb-324">[ Blazor WebAssembly uygulamalar](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında komut satırı bağımsız değişkenleri olarak aşağıdaki ana bilgisayar yapılandırma değerlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-324">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="2d8cb-325">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="2d8cb-325">Content root</span></span>

<span data-ttu-id="2d8cb-326">`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-326">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="2d8cb-327">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-327">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="2d8cb-328">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-328">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2d8cb-329">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-329">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="2d8cb-330">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . </span><span class="sxs-lookup"><span data-stu-id="2d8cb-330">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="2d8cb-331">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-331">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="2d8cb-332">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-332">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2d8cb-333">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-333">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="2d8cb-334">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="2d8cb-334">Path base</span></span>

<span data-ttu-id="2d8cb-335">`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-335">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="2d8cb-336">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-336">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="2d8cb-337">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-337">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2d8cb-338">Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-338">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="2d8cb-339">Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-339">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="2d8cb-340">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-340">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2d8cb-341">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-341">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="2d8cb-342">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . </span><span class="sxs-lookup"><span data-stu-id="2d8cb-342">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="2d8cb-343">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-343">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="2d8cb-344">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-344">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2d8cb-345">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-345">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="2d8cb-346">URL’ler</span><span class="sxs-lookup"><span data-stu-id="2d8cb-346">URLs</span></span>

<span data-ttu-id="2d8cb-347">`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-347">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="2d8cb-348">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-348">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2d8cb-349">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-349">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="2d8cb-350">IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` . </span><span class="sxs-lookup"><span data-stu-id="2d8cb-350">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="2d8cb-351">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-351">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="2d8cb-352">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-352">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2d8cb-353">Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-353">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="2d8cb-354">Kesiciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-354">Configure the Trimmer</span></span>

<span data-ttu-id="2d8cb-355">Blazor çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) kırpılmasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-355">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="2d8cb-356">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-356">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="2d8cb-357">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-357">Configure the Linker</span></span>

<span data-ttu-id="2d8cb-358">Blazor çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-358">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="2d8cb-359">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-359">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="2d8cb-360">Özel önyükleme kaynağı yükleme</span><span class="sxs-lookup"><span data-stu-id="2d8cb-360">Custom boot resource loading</span></span>

<span data-ttu-id="2d8cb-361">Blazor WebAssemblyUygulama, `loadBootResource` yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için işleviyle başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-361">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="2d8cb-362">`loadBootResource`Aşağıdaki senaryolar için kullanın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-362">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="2d8cb-363">Kullanıcıların saat dilimi verileri veya bir CDN 'den statik kaynakları yüklemesine izin verin `dotnet.wasm` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-363">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="2d8cb-364">Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-364">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="2d8cb-365">Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-365">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="2d8cb-366">`loadBootResource` Parametreler aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-366">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="2d8cb-367">Parametre</span><span class="sxs-lookup"><span data-stu-id="2d8cb-367">Parameter</span></span>    | <span data-ttu-id="2d8cb-368">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d8cb-368">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="2d8cb-369">Kaynağın türü.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-369">The type of the resource.</span></span> <span data-ttu-id="2d8cb-370">İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="2d8cb-370">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="2d8cb-371">Kaynağın adı.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-371">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="2d8cb-372">Kaynağın göreli veya mutlak URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-372">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="2d8cb-373">Yanıtta beklenen içeriği temsil eden bütünlük dizesi.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-373">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="2d8cb-374">`loadBootResource` yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-374">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="2d8cb-375">URI dizesi.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-375">URI string.</span></span> <span data-ttu-id="2d8cb-376">Aşağıdaki örnekte ( `wwwroot/index.html` ), aşağıdaki dosyalar ' de BIR CDN 'den sunulur `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-376">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="2d8cb-377">Saat dilimi verileri</span><span class="sxs-lookup"><span data-stu-id="2d8cb-377">Timezone data</span></span>

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

* <span data-ttu-id="2d8cb-378">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-378">`Promise<Response>`.</span></span> <span data-ttu-id="2d8cb-379">`integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-379">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="2d8cb-380">Aşağıdaki örnek ( `wwwroot/index.html` ) giden isteklere özel bır http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-380">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="2d8cb-381">`null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-381">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="2d8cb-382">Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-382">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="2d8cb-383">CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-383">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="2d8cb-384">Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-384">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="2d8cb-385">İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-385">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="2d8cb-386">DLL dosyalarının dosya adı uzantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="2d8cb-386">Change the filename extension of DLL files</span></span>

<span data-ttu-id="2d8cb-387">Uygulamanın yayımlanmış dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa `.dll` , bu bölümdeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-387">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="2d8cb-388">Uygulamayı yayımladıktan sonra, `.dll` farklı bir dosya uzantısı kullanmak üzere dosyaları yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-388">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="2d8cb-389">`.dll` `wwwroot` Uygulamanın yayınlanan çıktısının dizinindeki dosyaları hedefleyin (örneğin, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-389">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="2d8cb-390">Aşağıdaki örneklerde `.dll` Dosyalar dosya uzantısını kullanacak şekilde yeniden adlandırılır `.bin` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-390">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="2d8cb-391">Windows'da:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-391">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="2d8cb-392">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-392">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="2d8cb-393">Linux veya macOS 'ta:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-393">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="2d8cb-394">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-394">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="2d8cb-395">Farklı bir dosya uzantısını kullanmak için `.bin` `.bin` Yukarıdaki komutlarda değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-395">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="2d8cb-396">Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları çözmek için `blazor.boot.json.br` aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-396">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="2d8cb-397">Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları kaldırın `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-397">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="2d8cb-398">Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-398">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="2d8cb-399">Güncelleştirilmiş dosyayı yeniden sıkıştırın `blazor.boot.json` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-399">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="2d8cb-400">Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-400">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="2d8cb-401">Ve ' i kaldırın veya yeniden sıkıştırın `wwwroot/service-worker-assets.js.br` `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-401">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="2d8cb-402">Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-402">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="2d8cb-403">Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-403">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="2d8cb-404">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-404">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="2d8cb-405">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-405">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="2d8cb-406">Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-406">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="2d8cb-407">Aynı derlemeleri yeniden adlandırırken ve geç yüklerken, içindeki kılavuza bakın <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-407">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="2d8cb-408">Bütünlük denetimi başarısızlıklarını çözümleyin</span><span class="sxs-lookup"><span data-stu-id="2d8cb-408">Resolve integrity check failures</span></span>

<span data-ttu-id="2d8cb-409">Blazor WebAssemblyUygulamanın başlangıç dosyalarını indirdiğinde, tarayıcıya yanıtlar üzerinde bütünlük denetimleri gerçekleştirmesini söyler.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-409">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="2d8cb-410">`blazor.boot.json` `.dll` , Ve diğer dosyalar IÇIN beklenen SHA-256 karma değerlerini belirtmek için dosyadaki bilgileri kullanır `.wasm` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-410">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="2d8cb-411">Bu, aşağıdaki nedenlerle faydalıdır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-411">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="2d8cb-412">Örneğin, Kullanıcı uygulama dosyalarını indirme sürecinde olduğunda, Web sunucunuza yeni bir dağıtım uygulanmışsa, tutarsız bir dosya kümesini yükleme riskini almanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-412">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="2d8cb-413">Tutarsız dosyalar tanımsız davranışa yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-413">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="2d8cb-414">Kullanıcının tarayıcısının hiçbir şekilde tutarsız veya geçersiz yanıtları önbelleklemesini sağlar, bu da sayfayı el ile yenilese bile uygulamayı başlatmalarını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-414">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="2d8cb-415">Yanıtları önbelleğe alma işlemini güvenli hale getirir ve beklenen SHA-256 karmaları değişene kadar sunucu tarafı değişikliklerini kontrol etmez, sonraki sayfa yüklemeleri daha az istek içerir ve çok daha hızlı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-415">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="2d8cb-416">Web sunucunuz beklenen SHA-256 karmalarıyla eşleşmeyen yanıtlar döndürürse, tarayıcının geliştirici konsolunda aşağıdakine benzer bir hata görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-416">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

> <span data-ttu-id="2d8cb-417"> https://myapp.example.com/\_framework/My Blazor Hesaplanan SHA-256 bütünlüğü ' IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY = ' ile 'App.dll ' kaynağı için ' Integrity ' özniteliğinde geçerli bir Özet bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-417">Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/\_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='.</span></span> <span data-ttu-id="2d8cb-418">Kaynak engellendi.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-418">The resource has been blocked.</span></span>

<span data-ttu-id="2d8cb-419">Çoğu durumda bu, bütünlük denetimi ile ilgili bir sorun *değildir* .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-419">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="2d8cb-420">Bunun yerine, başka bir sorun olduğu anlamına gelir ve bu sorunu gidermek için bütünlük denetimi size uyarı verebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-420">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="2d8cb-421">Bütünlük sorunlarını tanılama</span><span class="sxs-lookup"><span data-stu-id="2d8cb-421">Diagnosing integrity problems</span></span>

<span data-ttu-id="2d8cb-422">Bir uygulama oluşturulduğunda, oluşturulan `blazor.boot.json` bildirim, derleme çıktısının oluşturulduğu sırada önyükleme KAYNAKLARıNıZıN SHA-256 karmalarını (örneğin,, `.dll` `.wasm` ve diğer dosyaları) açıklar.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-422">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="2d8cb-423">' Deki SHA-256 karmaları `blazor.boot.json` tarayıcıya teslim edilen dosyalarla eşleştiği sürece bütünlük denetimi geçirilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-423">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="2d8cb-424">Bunun başarısız olmasının yaygın nedenleri:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-424">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="2d8cb-425">Web sunucusunun yanıtı, tarayıcı istenen dosya yerine bir hatadır (örneğin, bir *404-bulunamadı* veya *500-Internal Server Error*).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-425">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="2d8cb-426">Bu, tarayıcı tarafından bir bütünlük denetimi hatası olarak bildirilir ve yanıt hatası olarak değildir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-426">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="2d8cb-427">Dosya içeriğini tarayıcıya, dosyaların oluşturulması ve teslimi arasında değiştiren bir sorun.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-427">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="2d8cb-428">Bu durum oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-428">This might happen:</span></span>
   * <span data-ttu-id="2d8cb-429">Ya da yapı araçları derleme çıkışını el ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-429">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="2d8cb-430">Dağıtım işleminin bazı bir yönü dosyaları değiştirdiyseniz.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-430">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="2d8cb-431">Örneğin, git tabanlı bir dağıtım mekanizması kullanıyorsanız, Windows üzerinde dosya oluşturup Linux 'ta kullanıma alırsanız git 'in Windows stili satır sonlarını şeffaf olarak UNIX stili satır sonlarına dönüştürdüğünü göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-431">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="2d8cb-432">Dosya satır sonlarını değiştirmek için SHA-256 karmaları değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-432">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="2d8cb-433">Bu sorundan kaçınmak için, [ `.gitattributes` Yapı yapıtlarını `binary` dosya olarak değerlendirmek üzere](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-433">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="2d8cb-434">Web sunucusu, bu dosyaları sunma bölümünde dosya içeriğini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-434">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="2d8cb-435">Örneğin, bazı içerik dağıtım ağları (CDNs) otomatik olarak HTML 'yi [küçültmeye](xref:client-side/bundling-and-minification#minification) çalışır ve bu sayede onu değiştirir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-435">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="2d8cb-436">Bu tür özellikleri devre dışı bırakmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-436">You may need to disable such features.</span></span>

<span data-ttu-id="2d8cb-437">Bu durumun hangi durumlarda geçerli olduğunu tanılamak için:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-437">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="2d8cb-438">Hatanın hata iletisini okuyarak tetikleneceği dosyayı aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-438">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="2d8cb-439">Tarayıcınızın geliştirici araçlarını açın ve *ağ* sekmesine bakın. Gerekirse, istek ve yanıtların listesini görmek için sayfayı yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-439">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="2d8cb-440">Bu listedeki hatayı tetikleyen dosyayı bulun.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-440">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="2d8cb-441">Yanıttaki HTTP durum kodunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-441">Check the HTTP status code in the response.</span></span> <span data-ttu-id="2d8cb-442">Sunucu *200-Tamam* (veya başka bir 2xx durum kodu) dışında bir şey döndürürse, tanılamaya yönelik bir sunucu tarafı sorununuz vardır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-442">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="2d8cb-443">Örneğin, 403 durum kodu bir yetkilendirme sorunu olduğu anlamına gelirken durum kodu 500, sunucunun belirtilmeyen bir şekilde başarısız olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-443">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="2d8cb-444">Uygulamayı tanılamak ve onarmak için sunucu tarafı günlüklerine başvurun.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-444">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="2d8cb-445">Durum kodu kaynak için *200-Tamam* ise, tarayıcının geliştirici araçlarındaki yanıt içeriğine bakın ve içeriğin beklenen verilerle eşleşip eşleşmediğini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-445">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matches up with the data expected.</span></span> <span data-ttu-id="2d8cb-446">Örneğin, yaygın bir sorun, isteklerin `index.html` diğer dosyalar için bile verilerinizi döndürmesi için yönlendirmeyi yanlış yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-446">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="2d8cb-447">`.wasm`İsteklerin yanıtlarının, Weelsembly ikilileriyle ve `.dll` isteklerin yanıtlarının .NET Derleme ikili dosyalarına sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-447">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="2d8cb-448">Aksi takdirde, tanılamaya yönelik bir sunucu tarafı yönlendirme sorununuz vardır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-448">If not, you have a server-side routing problem to diagnose.</span></span>
 1. <span data-ttu-id="2d8cb-449">[Sorun giderme bütünlüğü PowerShell betiği](#troubleshoot-integrity-powershell-script)ile uygulamanın yayımlanmış ve dağıtılan çıkışını doğrulamak için arama yapın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-449">Seek to validate the app's published and deployed output with the [Troubleshoot integrity PowerShell script](#troubleshoot-integrity-powershell-script).</span></span>

<span data-ttu-id="2d8cb-450">Sunucunun sürekli doğru verileri döndürdüğünü onaylamak için, dosyanın oluşturulması ve teslimi arasındaki içerikleri değiştirmek için başka bir şey olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-450">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="2d8cb-451">Bunu araştırmak için:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-451">To investigate this:</span></span>

 * <span data-ttu-id="2d8cb-452">Dosyalar derlendikten sonra dosyaları değiştirirken derleme araç zinciri ve dağıtım mekanizmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-452">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="2d8cb-453">Git 'in, daha önce açıklandığı gibi dosya satır sonlarını dönüştürerken buna bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-453">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="2d8cb-454">Yanıtları dinamik olarak değiştirmek üzere ayarlanmış olmaları durumunda Web sunucusunu veya CDN yapılandırmasını inceleyin (örneğin, HTML 'yi küçültmeye çalışırken).</span><span class="sxs-lookup"><span data-stu-id="2d8cb-454">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="2d8cb-455">Web sunucusunun HTTP sıkıştırması uygulaması (örneğin, döndürme `content-encoding: br` veya döndürme `content-encoding: gzip` ), bu nedenle bu, sıkıştırmayı açma işleminden sonra sonucu etkilemez.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-455">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="2d8cb-456">Ancak, Web sunucusunun sıkıştırılmamış verileri değiştirmesi iyi *değildir* .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-456">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="troubleshoot-integrity-powershell-script"></a><span data-ttu-id="2d8cb-457">Bütünlük PowerShell betiği sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="2d8cb-457">Troubleshoot integrity PowerShell script</span></span>

<span data-ttu-id="2d8cb-458">[`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true)Yayımlanmış ve dağıtılan bir uygulamayı doğrulamak için PowerShell betiğini kullanın Blazor .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-458">Use the [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell script to validate a published and deployed Blazor app.</span></span> <span data-ttu-id="2d8cb-459">Komut dosyası, uygulamanın, Blazor Framework 'ün tanımlayamıyorum bütünlük sorunları olduğunda bir başlangıç noktası olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-459">The script is provided as a starting point when the app has integrity issues that the Blazor framework can't identify.</span></span> <span data-ttu-id="2d8cb-460">Uygulamanız için betiğin özelleştirilmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-460">Customization of the script might be required for your apps.</span></span>

<span data-ttu-id="2d8cb-461">Betik, klasördeki dosyaları denetler `publish` ve bütünlük karmalarını içeren farklı bildirimlerde sorunları algılamak için dağıtılan uygulamadan indirilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-461">The script checks the files in the `publish` folder and downloaded from the deployed app to detect issues in the different manifests that contain integrity hashes.</span></span> <span data-ttu-id="2d8cb-462">Bu denetimler en yaygın sorunları algılamamalıdır:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-462">These checks should detect the most common problems:</span></span>

* <span data-ttu-id="2d8cb-463">Yayımlanan çıktıda bir dosyayı gerçekleştirmeden değiştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-463">You modified a file in the published output without realizing it.</span></span>
* <span data-ttu-id="2d8cb-464">Uygulama, dağıtım hedefine doğru şekilde dağıtılmadı veya dağıtım hedefinin ortamında bir değişiklik yapılamayabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-464">The app wasn't correctly deployed to the deployment target, or something changed within the deployment target's environment.</span></span>
* <span data-ttu-id="2d8cb-465">Dağıtılan uygulamayla çıkış arasında uygulamanın yayımlaması arasında farklılıklar vardır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-465">There are differences between the deployed app and the output from publishing the app.</span></span>

<span data-ttu-id="2d8cb-466">PowerShell komut kabuğu 'nda aşağıdaki komutla betiği çağırın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-466">Invoke the script with the following command in a PowerShell command shell:</span></span>

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

<span data-ttu-id="2d8cb-467">İçeremez</span><span class="sxs-lookup"><span data-stu-id="2d8cb-467">Placeholders:</span></span>

* <span data-ttu-id="2d8cb-468">`{BASE URL}`: Dağıtılan uygulamanın URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-468">`{BASE URL}`: The URL of the deployed app.</span></span>
* <span data-ttu-id="2d8cb-469">`{PUBLISH OUTPUT FOLDER}`: Uygulamanın `publish` dağıtım için yayımlandığı klasörün veya konumun yolu.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-469">`{PUBLISH OUTPUT FOLDER}`: The path to the app's `publish` folder or location where the app is published for deployment.</span></span>

> [!NOTE]
> <span data-ttu-id="2d8cb-470">`dotnet/AspNetCore.Docs`GitHub deposunu [BitDefender](https://www.bitdefender.com) virüs tarayıcısını kullanan bir sisteme kopyalamak için, komut dosyası için BitDefender 'a bir özel durum ekleyin `integrity.ps1` .</span><span class="sxs-lookup"><span data-stu-id="2d8cb-470">To clone the `dotnet/AspNetCore.Docs` GitHub repository to a system that uses the [Bitdefender](https://www.bitdefender.com) virus scanner, add an exception to Bitdefender for the `integrity.ps1` script.</span></span> <span data-ttu-id="2d8cb-471">Betiği, virüs tarayıcısı tarafından karantinaya almadan önce depoyu kopyalamadan önce BitDefender ' a özel durum ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-471">Add the exception to Bitdefender before cloning the repo to avoid having the script quarantined by the virus scanner.</span></span> <span data-ttu-id="2d8cb-472">Aşağıdaki örnek, bir Windows sistemindeki kopyalanmış depo için bir komut dosyasının tipik yoludur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-472">The following example is a typical path to the script for the cloned repo on a Windows system.</span></span> <span data-ttu-id="2d8cb-473">Yolu gereken şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-473">Adjust the path as needed.</span></span> <span data-ttu-id="2d8cb-474">Yer tutucu, `{USER}` kullanıcının yol segmentinde yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-474">The placeholder `{USER}` is the user's path segment.</span></span>
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="2d8cb-475">PWA olmayan uygulamalar için bütünlük denetimini devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="2d8cb-475">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="2d8cb-476">Çoğu durumda, bütünlük denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-476">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="2d8cb-477">Bütünlük denetimini devre dışı bırakmak, beklenmeyen yanıtlara neden olan temeldeki sorunu çözmez ve daha önce listelenen avantajları kaybetme sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-477">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="2d8cb-478">Web sunucusunun tutarlı yanıtlar döndürecek şekilde güvenmeme, ancak hiçbir seçim olmadığı ancak bütünlük denetimlerini devre dışı bırakabileceği durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-478">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="2d8cb-479">Bütünlük denetimlerini devre dışı bırakmak için aşağıdakini proje dosyasındaki bir özellik grubuna ekleyin Blazor WebAssembly `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="2d8cb-479">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="2d8cb-480">`BlazorCacheBootResources`Blazor `.dll` , `.wasm` özelliği SHA-256 karmalarını temel alarak,, ve diğer dosyaları önbelleğe almanın varsayılan davranışını devre dışı bırakır çünkü özellik SHA-256 karmalarını doğruluk açısından güvenemediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-480">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="2d8cb-481">Bu ayar ile birlikte, tarayıcının normal HTTP önbelleği bu dosyaları önbelleğe almaya devam edebilir, ancak bunun gerçekleşmediğine bakılmaksızın Web sunucusu yapılandırmanıza ve `cache-control` hizmet verdiği üstbilgilere göre değişir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-481">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="2d8cb-482">`BlazorCacheBootResources`Özelliği, [aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)için bütünlük denetimlerini devre dışı bırakmıyor.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-482">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="2d8cb-483">PWAs ile ilgili rehberlik için [PWAs için bütünlük denetimini devre dışı bırakma](#disable-integrity-checking-for-pwas) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-483">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="2d8cb-484">PWAs için bütünlük denetimini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="2d8cb-484">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="2d8cb-485">BlazorAşamalı Web uygulaması (PWA) şablonu, `service-worker.published.js` çevrimdışı kullanım için uygulama dosyalarını getirmekten ve saklamaktan sorumlu önerilen bir dosya içerir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-485">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="2d8cb-486">Bu, normal uygulama başlangıç mekanizmasından ayrı bir işlemdir ve kendi ayrı bütünlük denetimi mantığına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-486">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="2d8cb-487">Dosya içinde `service-worker.published.js` Aşağıdaki satır mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-487">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="2d8cb-488">Bütünlük denetimini devre dışı bırakmak için, `integrity` satırı aşağıdaki şekilde değiştirerek parametreyi kaldırın:</span><span class="sxs-lookup"><span data-stu-id="2d8cb-488">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="2d8cb-489">Daha sonra, bütünlük denetimini devre dışı bırakmak, bütünlük denetimi tarafından sunulan güvenlik garantisi kaybetmeniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-489">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="2d8cb-490">Örneğin, kullanıcının tarayıcısı uygulamayı yeni bir sürümünü dağıttığınız andan itibaren önbelleğe alıyorsa, eski dağıtımdan ve bazıları yeni dağıtımdan bazı dosyaları önbelleğe alabilir, bu da bir risk vardır.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-490">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="2d8cb-491">Bu durumda, daha fazla güncelleştirme dağıtana kadar uygulama bozuk bir durumda takılmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="2d8cb-491">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
