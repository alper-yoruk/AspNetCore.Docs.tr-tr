---
title: ASP.NET Core'da Tek Sayfauygulamaları Oluşturmak için JavaScript Hizmetlerini Kullanın
author: scottaddie
description: ASP.NET Core tarafından desteklenen tek sayfalık bir Uygulama (SPA) oluşturmak için JavaScript Hizmetlerini kullanmanın yararları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663781"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="d7685-103">ASP.NET Core'da Tek Sayfauygulamaları Oluşturmak için JavaScript Hizmetlerini Kullanın</span><span class="sxs-lookup"><span data-stu-id="d7685-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="d7685-104">Scott [Addie](https://github.com/scottaddie) ve [Fiyaz Hasan](https://fiyazhasan.me/) tarafından</span><span class="sxs-lookup"><span data-stu-id="d7685-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="d7685-105">Tek Sayfa uygulaması (SPA), doğası nda olan zengin kullanıcı deneyimi nedeniyle popüler bir web uygulaması türüdür.</span><span class="sxs-lookup"><span data-stu-id="d7685-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="d7685-106">Istemci tarafındaki SPA çerçevelerini veya [Açısal](https://angular.io/) veya [React](https://facebook.github.io/react/)gibi kitaplıkları ASP.NET Core gibi sunucu tarafındaki çerçevelerle tümleştirmek zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="d7685-107">JavaScript Hizmetleri entegrasyon sürecinde sürtünmeyi azaltmak için geliştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d7685-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="d7685-108">Farklı istemci ve sunucu teknolojisi yığınları arasında sorunsuz çalışma sağlar.</span><span class="sxs-lookup"><span data-stu-id="d7685-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="d7685-109">Bu makalede açıklanan özellikler, ASP.NET Core 3.0 itibariyle geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="d7685-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="d7685-110">Daha basit bir SPA çerçeveleri tümleştirme mekanizması [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet paketinde mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="d7685-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="d7685-111">Daha fazla bilgi için [bkz: [Duyuru] Obsoleting Microsoft.AspNetCore.SpaServices ve Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="d7685-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="d7685-112">JavaScript Hizmetleri Nedir</span><span class="sxs-lookup"><span data-stu-id="d7685-112">What is JavaScript Services</span></span>

<span data-ttu-id="d7685-113">JavaScript Hizmetleri, ASP.NET Core için istemci tarafı teknolojilerinin bir koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="d7685-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="d7685-114">Amacı, ASP.NET Core'u geliştiricilerin SP'leri oluşturmak için tercih ettiği sunucu tarafı platformu olarak konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="d7685-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="d7685-115">JavaScript Hizmetleri iki farklı NuGet paketinden oluşur:</span><span class="sxs-lookup"><span data-stu-id="d7685-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="d7685-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="d7685-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="d7685-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="d7685-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="d7685-118">Bu paketler aşağıdaki senaryolarda yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="d7685-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="d7685-119">JavaScript'i sunucuda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="d7685-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="d7685-120">SPA çerçevesi veya kitaplık kullanma</span><span class="sxs-lookup"><span data-stu-id="d7685-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="d7685-121">Webpack ile istemci tarafı varlıklar oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7685-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="d7685-122">Bu makaledeki odak noktası nın çoğu SpaServices paketinin kullanılmasıdır.</span><span class="sxs-lookup"><span data-stu-id="d7685-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="d7685-123">SpaServices Nedir</span><span class="sxs-lookup"><span data-stu-id="d7685-123">What is SpaServices</span></span>

<span data-ttu-id="d7685-124">SpaServices, ASP.NET Core'u geliştiricilerin SP'leri oluşturmak için tercih ettiği sunucu tarafı platformu olarak konumlandırmak için oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="d7685-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="d7685-125">SpaServices ASP.NET Core ile SP'ler geliştirmek için gerekli değildir ve geliştiricileri belirli bir istemci çerçevesine kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="d7685-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="d7685-126">SpaServices aşağıdakiler gibi yararlı altyapı sağlar:</span><span class="sxs-lookup"><span data-stu-id="d7685-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="d7685-127">Sunucu tarafı ön oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7685-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="d7685-128">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="d7685-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="d7685-129">Sıcak Modül Değişimi</span><span class="sxs-lookup"><span data-stu-id="d7685-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="d7685-130">Yönlendirme yardımcıları</span><span class="sxs-lookup"><span data-stu-id="d7685-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="d7685-131">Bu altyapı bileşenleri toplu olarak hem geliştirme iş akışını hem de çalışma zamanı deneyimini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="d7685-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="d7685-132">Bileşenler ayrı ayrı kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="d7685-133">SpaServices kullanmak için ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d7685-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="d7685-134">SpaServices ile çalışmak için aşağıdakileri yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d7685-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="d7685-135">[Node.js](https://nodejs.org/) (sürüm 6 veya sonraki) npm ile</span><span class="sxs-lookup"><span data-stu-id="d7685-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="d7685-136">Bu bileşenlerin yüklü olduğunu ve bulunabileceğini doğrulamak için komut satırından aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d7685-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="d7685-137">Bir Azure web sitesine dağıtılıyorsanız,&mdash;Node.js yüklü ve sunucu ortamlarında kullanılabilir bir eylem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d7685-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="d7685-138">Visual Studio 2017'yi kullanan Windows'da SDK, **.NET Core çapraz platform geliştirme** iş yükünü seçerek yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d7685-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="d7685-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="d7685-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="d7685-140">Sunucu tarafı ön oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7685-140">Server-side prerendering</span></span>

<span data-ttu-id="d7685-141">Evrensel (izomorfik olarak da bilinir) uygulama, hem sunucuda hem de istemcide çalıştırabilen bir JavaScript uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="d7685-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="d7685-142">Açısal, React ve diğer popüler çerçeveler bu uygulama geliştirme stili için evrensel bir platform sağlar.</span><span class="sxs-lookup"><span data-stu-id="d7685-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="d7685-143">Fikir, çerçeve bileşenlerini önce Node.js üzerinden sunucuda işlemek ve daha sonra daha fazla yürütmeyi istemciye devretmektir.</span><span class="sxs-lookup"><span data-stu-id="d7685-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="d7685-144">SpaServices tarafından sağlanan ASP.NET Çekirdek [Etiket Yardımcıları,](xref:mvc/views/tag-helpers/intro) sunucudaki JavaScript işlevlerini çağırarak sunucu tarafında ön oluşturma uygulamasını basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="d7685-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="d7685-145">Sunucu tarafı önoluşturma önkoşulları</span><span class="sxs-lookup"><span data-stu-id="d7685-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="d7685-146">[aspnet önişleme](https://www.npmjs.com/package/aspnet-prerendering) npm paketini yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d7685-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="d7685-147">Sunucu tarafı önoluşturma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d7685-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="d7685-148">Tag Yardımcıları, projenin *_ViewImports.cshtml* dosyasındaki ad alanı kaydı ile bulunabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="d7685-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="d7685-149">Bu Tag Yardımcıları, Razor görünümü nde HTML benzeri bir sözdiziminden yararlanarak düşük seviyeli API'lerle doğrudan iletişim kurmanın inceliklerini özetler:</span><span class="sxs-lookup"><span data-stu-id="d7685-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="d7685-150">asp-prerender-modül Etiket Yardımcı</span><span class="sxs-lookup"><span data-stu-id="d7685-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="d7685-151">Önceki `asp-prerender-module` kod örneğinde kullanılan Etiket Yardımcısı, Node.js aracılığıyla sunucuda *ClientApp/dist/main-server.js* yürütür.</span><span class="sxs-lookup"><span data-stu-id="d7685-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="d7685-152">Netlik aşkına, *main-server.js* dosyası [Webpack](https://webpack.github.io/) oluşturma işleminde TypeScript-to-JavaScript transpilation task bir artifakı olduğunu.</span><span class="sxs-lookup"><span data-stu-id="d7685-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="d7685-153">Webpack bir giriş noktası diğer `main-server`ad tanımlar; ve bu diğer ad için bağımlılık grafiğinin geçişi *ClientApp/boot-server.ts* dosyasında başlar:</span><span class="sxs-lookup"><span data-stu-id="d7685-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="d7685-154">Aşağıdaki Açısal örnekte, *ClientApp/boot-server.ts* dosyası, `createServerRenderer` Node.js `aspnet-prerendering` üzerinden sunucu oluşturmayı yapılandırmak için npm paketinin işlevini ve `RenderResult` türünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="d7685-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="d7685-155">Sunucu tarafı oluşturma için ayrılan HTML biçimlendirmesi, güçlü bir şekilde yazılmış bir JavaScript `Promise` nesnesine sarılmış bir çözüm işlevi çağrısına aktarılır.</span><span class="sxs-lookup"><span data-stu-id="d7685-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="d7685-156">Nesnenin `Promise` önemi, HTML biçimlendirmesini DOM'un yer tutucu öğesinde enjeksiyon için sayfaya eş senkronize olarak tesbit etmesidir.</span><span class="sxs-lookup"><span data-stu-id="d7685-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="d7685-157">asp-prerender-data Tag Helper</span><span class="sxs-lookup"><span data-stu-id="d7685-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="d7685-158">`asp-prerender-module` Tag Helper ile birleştiğinde, Tag Helper, Razor görünümünden sunucu tarafındaki JavaScript'e bağlamsal bilgileri aktarmak için `asp-prerender-data` kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="d7685-159">Örneğin, aşağıdaki biçimlendirme kullanıcı verilerini `main-server` modüle geçirir:</span><span class="sxs-lookup"><span data-stu-id="d7685-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="d7685-160">Alınan `UserName` bağımsız değişken yerleşik JSON serilandırıcı kullanılarak seri hale getirilmiştir `params.data` ve nesnede depolanır.</span><span class="sxs-lookup"><span data-stu-id="d7685-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="d7685-161">Aşağıdaki Açısal örnekte, veriler bir `h1` öğe içinde kişiselleştirilmiş bir karşılama oluşturmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d7685-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="d7685-162">Tag Helpers'da geçirilen özellik adları **PascalCase** gösterimi ile temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="d7685-163">Aynı özellik adlarının **camelCase**ile temsil edildiği JavaScript ile karşıtlık.</span><span class="sxs-lookup"><span data-stu-id="d7685-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="d7685-164">Varsayılan JSON serileştirme yapılandırması bu farkın sorumlusudur.</span><span class="sxs-lookup"><span data-stu-id="d7685-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="d7685-165">Önceki kod örneğini genişletmek için, veriler `globals` `resolve` işleve sağlanan özelliği nemlendirerek sunucudan görünüme aktarılabilir:</span><span class="sxs-lookup"><span data-stu-id="d7685-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="d7685-166">Nesneiçinde `postList` `globals` tanımlanan dizi tarayıcının genel `window` nesnesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="d7685-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="d7685-167">Bu değişken in global kapsama çekilmesi, özellikle aynı verileri sunucuya ve istemciye bir kez yüklemeyle ilgili olduğundan, çabanın çoğaltılmasını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="d7685-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![pencere nesnesine bağlı global postList değişkeni](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="d7685-169">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="d7685-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="d7685-170">[Webpack Dev Middleware, Webpack'in](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) talep üzerine kaynak oluşturduğu kolaylaştırılmış bir geliştirme iş akışı sunar.</span><span class="sxs-lookup"><span data-stu-id="d7685-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="d7685-171">Bir sayfa tarayıcıda yeniden yüklendiğinde ara yazılım istemci tarafındaki kaynakları otomatik olarak derler ve hizmet vermektedir.</span><span class="sxs-lookup"><span data-stu-id="d7685-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="d7685-172">Alternatif yaklaşım, bir üçüncü taraf bağımlılık veya özel kod değiştiğinde projenin npm yapı komut dosyası üzerinden Webpack'i el ile çağırmaktır.</span><span class="sxs-lookup"><span data-stu-id="d7685-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="d7685-173">*package.json* dosyasındaki bir npm yapı komut dosyası aşağıdaki örnekte gösterilir:</span><span class="sxs-lookup"><span data-stu-id="d7685-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="d7685-174">Webpack Dev Middleware ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d7685-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="d7685-175">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm paketini yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d7685-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="d7685-176">Webpack Dev Middleware yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d7685-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="d7685-177">Webpack Dev Middleware, *Startup.cs* dosyasının `Configure` yönteminde aşağıdaki kod aracılığıyla HTTP istek ardışık hattına kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="d7685-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="d7685-178">Uzantı `UseWebpackDevMiddleware` yöntemi ile `UseStaticFiles` [statik dosya barındırma kaydetmeden](xref:fundamentals/static-files) önce uzantısı yöntemi çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d7685-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="d7685-179">Güvenlik nedenleriyle, ara yazılımı yalnızca uygulama geliştirme modunda çalıştığında kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d7685-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="d7685-180">*Webpack.config.js* dosyasının `output.publicPath` özelliği, ara yazılıma `dist` değişiklikler için klasörü izlemelerini söyler:</span><span class="sxs-lookup"><span data-stu-id="d7685-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="d7685-181">Sıcak Modül Değişimi</span><span class="sxs-lookup"><span data-stu-id="d7685-181">Hot Module Replacement</span></span>

<span data-ttu-id="d7685-182">Webpack'in [Sıcak Modül Değiştirme](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) özelliğini [Webpack Dev Middleware'in](#webpack-dev-middleware)bir evrimi olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="d7685-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="d7685-183">HMR tüm aynı avantajları sunar, ancak değişiklikleri derledikten sonra sayfa içeriğini otomatik olarak güncelleyerek geliştirme iş akışını daha da kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="d7685-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="d7685-184">Bunu, SPA'nın geçerli bellek içi durumu ve hata ayıklama oturumunu engelleyecek tarayıcının yenilenmesiyle karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="d7685-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="d7685-185">Webpack Dev Middleware hizmeti ile tarayıcı arasında canlı bir bağlantı vardır, bu da değişikliklerin tarayıcıya itildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d7685-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="d7685-186">Sıcak Modül Değiştirme ön koşulları</span><span class="sxs-lookup"><span data-stu-id="d7685-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="d7685-187">[Webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm paketini yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d7685-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="d7685-188">Sıcak Modül Değiştirme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d7685-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="d7685-189">HMR bileşeni yöntemde MVC'nin HTTP istek `Configure` ardışık hattına kaydedilmelidir:</span><span class="sxs-lookup"><span data-stu-id="d7685-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="d7685-190">[Webpack Dev Middleware'de](#webpack-dev-middleware)olduğu `UseWebpackDevMiddleware` gibi, uzantı `UseStaticFiles` yöntemi uzatma yönteminden önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d7685-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="d7685-191">Güvenlik nedenleriyle, ara yazılımı yalnızca uygulama geliştirme modunda çalıştığında kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d7685-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="d7685-192">*Webpack.config.js* dosyası boş `plugins` bırakılmış olsa bile bir dizi tanımlamalıdır:</span><span class="sxs-lookup"><span data-stu-id="d7685-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="d7685-193">Uygulamayı tarayıcıya yükledikten sonra, geliştirici araçlarının Konsol sekmesi HMR aktivasyonunun onayını sağlar:</span><span class="sxs-lookup"><span data-stu-id="d7685-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Sıcak Modül Değiştirme bağlı mesaj](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="d7685-195">Yönlendirme yardımcıları</span><span class="sxs-lookup"><span data-stu-id="d7685-195">Routing helpers</span></span>

<span data-ttu-id="d7685-196">Çoğu ASP.NET Core tabanlı SP'lerde, istemci tarafı yönlendirmegenellikle sunucu tarafı yönlendirmeye ek olarak istenir.</span><span class="sxs-lookup"><span data-stu-id="d7685-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="d7685-197">SPA ve MVC yönlendirme sistemleri müdahale olmadan bağımsız olarak çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="d7685-198">Ancak, zorluklar alabilmeniz için bir kenar durumu vardır: 404 HTTP yanıtlarını tanımlamak.</span><span class="sxs-lookup"><span data-stu-id="d7685-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="d7685-199">Uzantısız bir rotanın kullanıldığı `/some/page` senaryoyu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="d7685-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="d7685-200">İsteğin sunucu tarafındaki bir rotayla eşleşmediğini, ancak deseni istemci tarafındaki bir rotayla eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="d7685-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="d7685-201">Şimdi genellikle sunucuda `/images/user-512.png`bir görüntü dosyası bulmak için bekliyor , için gelen bir istek düşünün.</span><span class="sxs-lookup"><span data-stu-id="d7685-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="d7685-202">İstenen bu kaynak yolu herhangi bir sunucu tarafı rotası veya statik dosyayla eşleşmiyorsa, istemci tarafındaki uygulamanın&mdash;genellikle 404 HTTP durum kodunu döndürerek işlemesi olası değildir.</span><span class="sxs-lookup"><span data-stu-id="d7685-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="d7685-203">Yönlendirme yardımcıları ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d7685-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="d7685-204">İstemci tarafı yönlendirme npm paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="d7685-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="d7685-205">Açısal'ı örnek olarak kullanma:</span><span class="sxs-lookup"><span data-stu-id="d7685-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="d7685-206">Yardımcı yapılandırmayı yönlendirme</span><span class="sxs-lookup"><span data-stu-id="d7685-206">Routing helpers configuration</span></span>

<span data-ttu-id="d7685-207">Yöntemde adlandırılmış bir `MapSpaFallbackRoute` uzantı yöntemi kullanılır: `Configure`</span><span class="sxs-lookup"><span data-stu-id="d7685-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="d7685-208">Rotalar, yapılandırıldıkları sırada değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="d7685-209">Sonuç olarak, `default` önceki kod örneğindeki rota önce desen eşleştirme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d7685-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="d7685-210">Yeni bir proje oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7685-210">Create a new project</span></span>

<span data-ttu-id="d7685-211">JavaScript Hizmetleri önceden yapılandırılmış uygulama şablonları sağlar.</span><span class="sxs-lookup"><span data-stu-id="d7685-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="d7685-212">SpaServices bu şablonlarda Açısal, React ve Redux gibi farklı çerçeveler ve kitaplıklarla birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d7685-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="d7685-213">Bu şablonlar aşağıdaki komutu çalıştırarak .NET Core CLI üzerinden yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="d7685-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="d7685-214">Kullanılabilir SPA şablonlarının listesi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d7685-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="d7685-215">Şablonlar</span><span class="sxs-lookup"><span data-stu-id="d7685-215">Templates</span></span>                                 | <span data-ttu-id="d7685-216">Kısa Ad</span><span class="sxs-lookup"><span data-stu-id="d7685-216">Short Name</span></span> | <span data-ttu-id="d7685-217">Dil</span><span class="sxs-lookup"><span data-stu-id="d7685-217">Language</span></span> | <span data-ttu-id="d7685-218">Etiketler</span><span class="sxs-lookup"><span data-stu-id="d7685-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="d7685-219">Açısal ASP.NET Çekirdek</span><span class="sxs-lookup"><span data-stu-id="d7685-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="d7685-220">Açısal</span><span class="sxs-lookup"><span data-stu-id="d7685-220">angular</span></span>    | <span data-ttu-id="d7685-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="d7685-221">[C#]</span></span>     | <span data-ttu-id="d7685-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="d7685-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="d7685-223">React.js ile MVC ASP.NET Çekirdek</span><span class="sxs-lookup"><span data-stu-id="d7685-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="d7685-224">Tepki</span><span class="sxs-lookup"><span data-stu-id="d7685-224">react</span></span>      | <span data-ttu-id="d7685-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="d7685-225">[C#]</span></span>     | <span data-ttu-id="d7685-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="d7685-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="d7685-227">React.js ve Redux ile MVC ASP.NET Çekirdek</span><span class="sxs-lookup"><span data-stu-id="d7685-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="d7685-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="d7685-228">reactredux</span></span> | <span data-ttu-id="d7685-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="d7685-229">[C#]</span></span>     | <span data-ttu-id="d7685-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="d7685-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="d7685-231">SPA şablonlarından birini kullanarak yeni bir proje oluşturmak için, şablonun **kısa adını** [dotnet yeni](/dotnet/core/tools/dotnet-new) komutuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d7685-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="d7685-232">Aşağıdaki komut, sunucu tarafı için yapılandırılan ASP.NET Core MVC ile açısal bir uygulama oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d7685-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="d7685-233">Çalışma zamanı yapılandırma modunu ayarlama</span><span class="sxs-lookup"><span data-stu-id="d7685-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="d7685-234">İki birincil çalışma zamanı yapılandırma modu vardır:</span><span class="sxs-lookup"><span data-stu-id="d7685-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="d7685-235">**Geliştirme**:</span><span class="sxs-lookup"><span data-stu-id="d7685-235">**Development**:</span></span>
  * <span data-ttu-id="d7685-236">Hata ayıklamayı kolaylaştırmak için kaynak haritalar içerir.</span><span class="sxs-lookup"><span data-stu-id="d7685-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="d7685-237">Performans için istemci tarafı kodunu optimize etmez.</span><span class="sxs-lookup"><span data-stu-id="d7685-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="d7685-238">**Üretim**:</span><span class="sxs-lookup"><span data-stu-id="d7685-238">**Production**:</span></span>
  * <span data-ttu-id="d7685-239">Kaynak eşlemleri hariç tutar.</span><span class="sxs-lookup"><span data-stu-id="d7685-239">Excludes source maps.</span></span>
  * <span data-ttu-id="d7685-240">Birleştirme ve kıyma yoluyla istemci tarafı kodunu optimize eder.</span><span class="sxs-lookup"><span data-stu-id="d7685-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="d7685-241">ASP.NET Core, yapılandırma `ASPNETCORE_ENVIRONMENT` modunu depolamak için bir ortam değişkeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="d7685-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="d7685-242">Daha fazla bilgi için [bkz.](xref:fundamentals/environments#set-the-environment)</span><span class="sxs-lookup"><span data-stu-id="d7685-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="d7685-243">.NET Core CLI ile çalıştırın</span><span class="sxs-lookup"><span data-stu-id="d7685-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="d7685-244">Proje kökünde aşağıdaki komutu çalıştırarak gerekli NuGet ve npm paketlerini geri yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d7685-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="d7685-245">Uygulamayı oluşturun ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d7685-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="d7685-246">Uygulama [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost'ta başlar.</span><span class="sxs-lookup"><span data-stu-id="d7685-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="d7685-247">`http://localhost:5000` Tarayıcıda gezinirken açılış sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d7685-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="d7685-248">Visual Studio ile Çalıştır 2017</span><span class="sxs-lookup"><span data-stu-id="d7685-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="d7685-249">[dotnet yeni](/dotnet/core/tools/dotnet-new) komutu tarafından oluşturulan *.csproj* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="d7685-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="d7685-250">Gerekli NuGet ve npm paketleri proje açıldıktan sonra otomatik olarak geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d7685-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="d7685-251">Bu geri yükleme işlemi birkaç dakika kadar sürebilir ve uygulama tamamlandığında çalışmaya hazırdır.</span><span class="sxs-lookup"><span data-stu-id="d7685-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="d7685-252">Yeşil çalıştır düğmesine `Ctrl + F5`tıklayın veya basın ve tarayıcı uygulamanın açılış sayfasına açılır.</span><span class="sxs-lookup"><span data-stu-id="d7685-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="d7685-253">Uygulama [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost çalışır.</span><span class="sxs-lookup"><span data-stu-id="d7685-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="d7685-254">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="d7685-254">Test the app</span></span>

<span data-ttu-id="d7685-255">SpaServices şablonları [Karma](https://karma-runner.github.io/1.0/index.html) ve [Yasemin](https://jasmine.github.io/)kullanarak istemci tarafı testleri çalıştırmak için önceden yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d7685-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="d7685-256">Yasemin JavaScript için popüler bir birim test çerçevesi, Karma ise bu testler için bir test koşucusu.</span><span class="sxs-lookup"><span data-stu-id="d7685-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="d7685-257">Karma, [web paketi Dev Middleware](#webpack-dev-middleware) ile çalışacak şekilde yapılandırılmıştır, bu nedenle geliştiricinin her değişiklik yapıldığında testi durdurması ve çalıştırması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d7685-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="d7685-258">İster test çalışmasına karşı çalışan kod ister test çalışmasının kendisine karşı olsun, test otomatik olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="d7685-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="d7685-259">Açısal uygulamayı örnek olarak kullanarak, `CounterComponent` *counter.component.spec.ts* dosyasında iki Yasemin test çalışması zaten sağlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="d7685-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="d7685-260">*ClientApp* dizinindeki komut istemini açın.</span><span class="sxs-lookup"><span data-stu-id="d7685-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="d7685-261">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d7685-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="d7685-262">Komut dosyası *karma.conf.js* dosyasında tanımlanan ayarları okuyan Karma test koşucusu başlattı.</span><span class="sxs-lookup"><span data-stu-id="d7685-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="d7685-263">Diğer ayarların yanı sıra *karma.conf.js,* dizi aracılığıyla `files` yürütülecek test dosyalarını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="d7685-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="d7685-264">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="d7685-264">Publish the app</span></span>

<span data-ttu-id="d7685-265">Azure'da yayımlama hakkında daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/12474) bakın.</span><span class="sxs-lookup"><span data-stu-id="d7685-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="d7685-266">Oluşturulan istemci tarafı varlıkları ve yayınlanan ASP.NET Core yapılarını dağıtmaya hazır bir pakette birleştirmek hantal olabilir.</span><span class="sxs-lookup"><span data-stu-id="d7685-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="d7685-267">Neyse ki, SpaServices adlı `RunWebpack`özel bir MSBuild hedef ile tüm yayın süreci yönetir:</span><span class="sxs-lookup"><span data-stu-id="d7685-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="d7685-268">MSBuild hedefi aşağıdaki sorumluluklara sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d7685-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="d7685-269">npm paketlerini geri yükleyin.</span><span class="sxs-lookup"><span data-stu-id="d7685-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="d7685-270">Üçüncü taraf, istemci tarafı varlıkların üretim sınıfı bir yapı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7685-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="d7685-271">Özel istemci tarafı varlıklarının üretim sınıfı bir yapı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7685-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="d7685-272">Web paketi tarafından oluşturulan varlıkları yayımlama klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="d7685-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="d7685-273">Çalışırken MSBuild hedefi çağrılır:</span><span class="sxs-lookup"><span data-stu-id="d7685-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="d7685-274">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d7685-274">Additional resources</span></span>

* [<span data-ttu-id="d7685-275">Açısal Dokümanlar</span><span class="sxs-lookup"><span data-stu-id="d7685-275">Angular Docs</span></span>](https://angular.io/docs)
