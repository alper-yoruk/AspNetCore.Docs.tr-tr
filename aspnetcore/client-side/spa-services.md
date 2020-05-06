---
title: ASP.NET Core içinde tek sayfalı uygulamalar oluşturmak için JavaScript hizmetlerini kullanın
author: scottaddie
description: ASP.NET Core tarafından desteklenen tek sayfalı uygulama (SPA) oluşturmak için JavaScript Hizmetleri kullanmanın avantajları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 65bd5157bb3909f8352debcb1a6dfa7d888eec0e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769929"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="7bdf9-103">ASP.NET Core içinde tek sayfalı uygulamalar oluşturmak için JavaScript hizmetlerini kullanın</span><span class="sxs-lookup"><span data-stu-id="7bdf9-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="7bdf9-104">[Scott Ade](https://github.com/scottaddie) ve [fiyaz hasan](https://fiyazhasan.me/) tarafından</span><span class="sxs-lookup"><span data-stu-id="7bdf9-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="7bdf9-105">Tek sayfalı uygulama (SPA), kendi kendine ait zengin Kullanıcı deneyimi nedeniyle popüler bir Web uygulaması türüdür.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="7bdf9-106">ASP.NET Core gibi sunucu tarafı çerçeveleri ile, [angular](https://angular.io/) veya yanıt verme gibi ISTEMCI tarafı Spa [çerçevelerini veya kitaplıklarını](https://facebook.github.io/react/)tümleştirme zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="7bdf9-107">Tümleştirme sürecinde uçuşmayı azaltmak için JavaScript Hizmetleri geliştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="7bdf9-108">Farklı istemci ve sunucu teknoloji yığınları arasında sorunsuz işleme sunar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="7bdf9-109">Bu makalede açıklanan özellikler ASP.NET Core 3,0 itibariyle kullanımdan kalkmıştır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="7bdf9-110">[Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet paketinde daha basıt bir spa çerçeveleri tümleştirme mekanizması mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="7bdf9-111">Daha fazla bilgi için bkz. [Microsoft. aspnetcore. spaservices ve Microsoft. AspNetCore. NodeServices üzerinde kullanımdan bulunan [Duyuru]](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="7bdf9-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="7bdf9-112">JavaScript Hizmetleri nedir?</span><span class="sxs-lookup"><span data-stu-id="7bdf9-112">What is JavaScript Services</span></span>

<span data-ttu-id="7bdf9-113">JavaScript Hizmetleri, ASP.NET Core yönelik istemci tarafı teknolojilerinin bir koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="7bdf9-114">Amacı, ASP.NET Core, geliştiricilerin, maça 'Ları oluşturmak için tercih edilen sunucu tarafı platformu olarak konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="7bdf9-115">JavaScript Hizmetleri iki ayrı NuGet paketi içerir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="7bdf9-116">[Microsoft. AspNetCore. nodeservices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (nodeservices)</span><span class="sxs-lookup"><span data-stu-id="7bdf9-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="7bdf9-117">[Microsoft. AspNetCore. spaservices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (spaservices)</span><span class="sxs-lookup"><span data-stu-id="7bdf9-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="7bdf9-118">Bu paketler, aşağıdaki senaryolarda faydalıdır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="7bdf9-119">Sunucuda JavaScript çalıştırma</span><span class="sxs-lookup"><span data-stu-id="7bdf9-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="7bdf9-120">SPA çerçevesi veya kitaplığı kullanma</span><span class="sxs-lookup"><span data-stu-id="7bdf9-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="7bdf9-121">WebPack ile istemci tarafı varlıkları derleme</span><span class="sxs-lookup"><span data-stu-id="7bdf9-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="7bdf9-122">Bu makaledeki odağın çoğu, SpaServices paketi kullanılarak yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="7bdf9-123">Maça Hizmetleri nedir?</span><span class="sxs-lookup"><span data-stu-id="7bdf9-123">What is SpaServices</span></span>

<span data-ttu-id="7bdf9-124">İstenmeyen hizmetler ASP.NET Core, geliştiricilerin, maça 'Ları oluşturmak için tercih edilen sunucu tarafı platformu olarak konumlandırmak üzere oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="7bdf9-125">Istenmeyen hizmetler ASP.NET Core ile maça geliştirmek için gerekli değildir ve geliştiricileri belirli bir istemci çerçevesinde kilitlemez.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="7bdf9-126">SpaServices şu gibi yararlı bir altyapı sağlar:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="7bdf9-127">Sunucu tarafı prerendering</span><span class="sxs-lookup"><span data-stu-id="7bdf9-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="7bdf9-128">Web paketi geliştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="7bdf9-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="7bdf9-129">Sık kullanılan modül değiştirme</span><span class="sxs-lookup"><span data-stu-id="7bdf9-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="7bdf9-130">Yönlendirme Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="7bdf9-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="7bdf9-131">Toplu olarak, bu altyapı bileşenleri hem geliştirme iş akışını hem de çalışma zamanı deneyimini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="7bdf9-132">Bileşenler tek tek benimsemiş olabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="7bdf9-133">Spaservice kullanımı için Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7bdf9-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="7bdf9-134">SpaServices ile çalışmak için aşağıdakileri yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="7bdf9-135">NPM ile [Node. js](https://nodejs.org/) (sürüm 6 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="7bdf9-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="7bdf9-136">Bu bileşenlerin yüklendiğini doğrulamak ve bulunabilir olması için komut satırından aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="7bdf9-137">Bir Azure Web sitesine dağıtım yapıyorsanız hiçbir işlem gerekli&mdash;değildir. js, sunucu ortamlarında yüklenir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="7bdf9-138">Visual Studio 2017 kullanarak Windows 'da SDK, **.NET Core platformlar arası geliştirme** iş yükü seçilerek yüklenir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="7bdf9-139">[Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="7bdf9-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="7bdf9-140">Sunucu tarafı prerendering</span><span class="sxs-lookup"><span data-stu-id="7bdf9-140">Server-side prerendering</span></span>

<span data-ttu-id="7bdf9-141">Evrensel (isomorphic olarak da bilinir) uygulaması, hem sunucu hem de istemci üzerinde çalışan bir JavaScript uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="7bdf9-142">Angular, tepki verme ve diğer popüler çerçeveler, bu uygulama geliştirme stili için evrensel bir platform sağlar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="7bdf9-143">Fikir, önce Node. js aracılığıyla sunucuda çerçeve bileşenlerini işlemek ve ardından istemciye daha fazla yürütme devretmek.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="7bdf9-144">SpaServices tarafından sunulan ASP.NET Core [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucuda JavaScript işlevlerini çağırarak sunucu tarafı prerendering 'in uygulanmasını basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="7bdf9-145">Sunucu tarafı prerendering önkoşulları</span><span class="sxs-lookup"><span data-stu-id="7bdf9-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="7bdf9-146">[ASPNET-prerendering](https://www.npmjs.com/package/aspnet-prerendering) NPM paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="7bdf9-147">Sunucu tarafı prerendering yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7bdf9-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="7bdf9-148">Etiket Yardımcıları, projenin *_ViewImports. cshtml* dosyasında ad alanı kaydı aracılığıyla bulunabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="7bdf9-149">Bu etiket yardımcıları, Razor görünümü içinde HTML benzeri bir söz dizimini kullanarak doğrudan alt düzey API 'lerle iletişim kurmanın karmaşık özelliklerini soyutlar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="7bdf9-150">ASP-PreRender-Module etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="7bdf9-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="7bdf9-151">Yukarıdaki `asp-prerender-module` kod örneğinde kullanılan etiket Yardımcısı, Node. js aracılığıyla sunucuda *clientapp/Dist/Main-Server. js* ' yi yürütür.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="7bdf9-152">Netme 'nin sake, *Main-Server. js* dosyası, [WebPack](https://webpack.github.io/) derleme sürecinde TypeScript-to-JavaScript transpilation görevinin yapıtı.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="7bdf9-153">WebPack, `main-server`bir giriş noktası diğer adını tanımlar; ve, bu diğer ad için bağımlılık grafiğinin çapraz geçişi *clientapp/Boot-Server. TS* dosyasında başlar:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="7bdf9-154">Aşağıdaki angular örneğinde, *clientapp/Boot-Server. TS* dosyası, Node. js aracılığıyla `createServerRenderer` sunucu işlemesini `RenderResult` yapılandırmak için `aspnet-prerendering` NPM paketinin işlevini ve türünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="7bdf9-155">Sunucu tarafı işlemeye yönelik HTML biçimlendirmesi, kesin türü belirtilmiş bir JavaScript `Promise` nesnesine Sarmalanan bir Resolve işlev çağrısına geçirilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="7bdf9-156">`Promise` Nesnenin önemı, Dom 'ın yer tutucu öğesine ekleme IÇIN sayfaya HTML işaretlemesini zaman uyumsuz olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="7bdf9-157">ASP-PreRender-veri etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="7bdf9-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="7bdf9-158">`asp-prerender-module` Etiket Yardımcısı ile birlikte kullanıldığında, Razor görünümündeki `asp-prerender-data` bağlama bilgilerini sunucu tarafı JavaScript 'e geçirmek için etiket Yardımcısı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="7bdf9-159">Örneğin, aşağıdaki biçimlendirme Kullanıcı verilerini `main-server` modüle geçirir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="7bdf9-160">Alınan `UserName` bağımsız DEĞIŞKEN yerleşik JSON seri hale getirici kullanılarak serileştirilir ve `params.data` nesnesinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="7bdf9-161">Aşağıdaki angular örneğinde, verileri bir `h1` öğesi içinde kişiselleştirilmiş bir selamlama oluşturmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="7bdf9-162">Etiket yardımcılarının geçirildiği Özellik adları **PascalCase** gösterimi ile temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="7bdf9-163">Aynı özellik adlarının **camelCase**ile temsil edildiği JavaScript 'e kontrast.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="7bdf9-164">Varsayılan JSON serileştirme yapılandırması, bu farkından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="7bdf9-165">Yukarıdaki kod örneğini genişletmek için, `globals` `resolve` işlevine verilen özelliği hibir şekilde görüntüleyerek, veriler sunucudan görünüme geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="7bdf9-166">Nesnenin içinde tanımlanan `postList` dizi tarayıcının genel `window` nesnesine iliştirilir. `globals`</span><span class="sxs-lookup"><span data-stu-id="7bdf9-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="7bdf9-167">Genel kapsama açık olan bu değişken, özellikle de aynı verileri bir kez sunucuya ve istemciye yeniden yüklemeye yönelik olarak çabaların çoğaltılmasını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![pencere nesnesine eklenen genel postList değişkeni](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="7bdf9-169">Web paketi geliştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="7bdf9-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="7bdf9-170">[Web paketi geliştirme ara yazılımı](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) , Web paketinin kaynakları isteğe bağlı olarak oluşturmakta olan kolaylaştırılmış bir geliştirme iş akışı</span><span class="sxs-lookup"><span data-stu-id="7bdf9-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="7bdf9-171">Yazılım, tarayıcıda bir sayfa yeniden yüklendiğinde istemci tarafı kaynaklarını otomatik olarak derler ve sunar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="7bdf9-172">Alternatif yaklaşım, üçüncü taraf bir bağımlılık veya özel kod değiştiğinde, projenin NPM derleme betiği aracılığıyla WebPack 'i el ile çağırmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="7bdf9-173">*Package. JSON* dosyasındaki NPM derleme betiği aşağıdaki örnekte gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="7bdf9-174">WebPack dev ara yazılım önkoşulları</span><span class="sxs-lookup"><span data-stu-id="7bdf9-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="7bdf9-175">[ASPNET-WebPack](https://www.npmjs.com/package/aspnet-webpack) NPM paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="7bdf9-176">WebPack dev ara yazılım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7bdf9-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="7bdf9-177">Web paketi geliştirme ara yazılımı, *Startup.cs* dosyasının `Configure` YÖNTEMINDE aşağıdaki kod aracılığıyla http istek ardışık düzenine kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="7bdf9-178">Uzantı `UseWebpackDevMiddleware` yöntemi aracılığıyla `UseStaticFiles` [statik dosya barındırma](xref:fundamentals/static-files) kaydedilmeden önce genişletme yöntemi çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="7bdf9-179">Güvenlik nedenleriyle, yalnızca uygulama geliştirme modunda çalışırken ara yazılımı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="7bdf9-180">*WebPack. config. js* dosyasının `output.publicPath` özelliği, ara yazılımlar için `dist` klasörü değişiklikleri izlemesini söyler:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="7bdf9-181">Sık kullanılan modül değiştirme</span><span class="sxs-lookup"><span data-stu-id="7bdf9-181">Hot Module Replacement</span></span>

<span data-ttu-id="7bdf9-182">WebPack [geliştirme ara yazılımı](#webpack-dev-middleware)'nın bir evrimi olarak Web paketi 'Nin [sık kullanılan modül değiştirme](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) özelliğini düşünün.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="7bdf9-183">HMR aynı avantajları sunar, ancak değişiklikleri derledikten sonra sayfa içeriğini otomatik olarak güncelleştirerek geliştirme iş akışını daha da kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="7bdf9-184">Bunu tarayıcı yenilemesine karıştırmayın, bu da geçerli bellek içi durumu ve SPA 'nın hata ayıklama oturumunu kesintiye uğratır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="7bdf9-185">Web paketi geliştirme ara yazılımı hizmeti ile tarayıcı arasında canlı bir bağlantı vardır ve bu, değişikliklerin tarayıcıya gönderildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="7bdf9-186">Sık kullanılan modül değiştirme önkoşulları</span><span class="sxs-lookup"><span data-stu-id="7bdf9-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="7bdf9-187">[WebPack-Hot-ara yazılım](https://www.npmjs.com/package/webpack-hot-middleware) NPM paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="7bdf9-188">Sık kullanılan modül değiştirme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7bdf9-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="7bdf9-189">HMR bileşeni, `Configure` metotta MVC 'nin http isteği ardışık düzeninde kayıtlı olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="7bdf9-190">[Web paketi geliştirme ara yazılımı](#webpack-dev-middleware)ile doğru olduğu için, `UseWebpackDevMiddleware` Genişletme yönteminin `UseStaticFiles` uzantı yönteminden önce çağrılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="7bdf9-191">Güvenlik nedenleriyle, yalnızca uygulama geliştirme modunda çalışırken ara yazılımı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="7bdf9-192">*Web paketi. config. js* dosyası boş bırakılmış olsa bile `plugins` bir dizi tanımlamalıdır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="7bdf9-193">Uygulamayı tarayıcıya yükledikten sonra, Geliştirici araçlarının konsol sekmesi HMR etkinleştirmenin onayını sağlar:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Sık kullanılan modül değiştirme bağlı iletisi](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="7bdf9-195">Yönlendirme Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="7bdf9-195">Routing helpers</span></span>

<span data-ttu-id="7bdf9-196">Çoğu ASP.NET Core tabanlı maça, istemci tarafı yönlendirme genellikle sunucu tarafı yönlendirmeye ek olarak istenir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="7bdf9-197">SPA ve MVC yönlendirme sistemleri, parazit olmadan bağımsız olarak çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="7bdf9-198">Ancak, bir sınır büyük/küçük harf sorunları: 404 HTTP yanıtlarını tanımlama.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="7bdf9-199">Uzantısız bir yolunun `/some/page` kullanıldığı senaryoyu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="7bdf9-200">İsteğin bir sunucu tarafı rotası ile eşleşmediğini, ancak deseninin bir istemci tarafı rotayla eşleştiğini varsayın.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="7bdf9-201">Bundan böyle `/images/user-512.png`, genellikle sunucusunda bir görüntü dosyası bulmayı bekleyen bir gelen isteği düşünün.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="7bdf9-202">İstenen kaynak yolu herhangi bir sunucu tarafı yolu veya statik dosya ile eşleşmiyorsa, istemci tarafı uygulamanın bu&mdash;işlemi genellikle BIR 404 HTTP durum kodu döndürerek işlemesi çok düşüktür.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="7bdf9-203">Yönlendirme Yardımcıları önkoşulları</span><span class="sxs-lookup"><span data-stu-id="7bdf9-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="7bdf9-204">İstemci tarafı yönlendirme NPM paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="7bdf9-205">Örnek olarak angular kullanma:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="7bdf9-206">Yönlendirme Yardımcıları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7bdf9-206">Routing helpers configuration</span></span>

<span data-ttu-id="7bdf9-207">`Configure` Yönteminde adlı `MapSpaFallbackRoute` uzantı yöntemi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="7bdf9-208">Yollar yapılandırıldıkları sırayla değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="7bdf9-209">Sonuç olarak, `default` önceki kod örneğinde yol, önce model eşleştirme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="7bdf9-210">Yeni bir proje oluşturma</span><span class="sxs-lookup"><span data-stu-id="7bdf9-210">Create a new project</span></span>

<span data-ttu-id="7bdf9-211">JavaScript Hizmetleri önceden yapılandırılmış uygulama şablonları sağlar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="7bdf9-212">Istenmeyen hizmetler, bu şablonlarda, angular, tepki ve Redux gibi farklı çerçeveler ve kitaplıklarla birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="7bdf9-213">Bu şablonlar, aşağıdaki komut çalıştırılarak .NET Core CLI aracılığıyla yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="7bdf9-214">Kullanılabilir SPA şablonlarının listesi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="7bdf9-215">Şablonlar</span><span class="sxs-lookup"><span data-stu-id="7bdf9-215">Templates</span></span>                                 | <span data-ttu-id="7bdf9-216">Kısa Ad</span><span class="sxs-lookup"><span data-stu-id="7bdf9-216">Short Name</span></span> | <span data-ttu-id="7bdf9-217">Dil</span><span class="sxs-lookup"><span data-stu-id="7bdf9-217">Language</span></span> | <span data-ttu-id="7bdf9-218">Etiketler</span><span class="sxs-lookup"><span data-stu-id="7bdf9-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="7bdf9-219">Angular ile MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7bdf9-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="7bdf9-220">Angular</span><span class="sxs-lookup"><span data-stu-id="7bdf9-220">angular</span></span>    | <span data-ttu-id="7bdf9-221">Þ</span><span class="sxs-lookup"><span data-stu-id="7bdf9-221">[C#]</span></span>     | <span data-ttu-id="7bdf9-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="7bdf9-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="7bdf9-223">ASP.NET Core, tepki verme. js ile MVC</span><span class="sxs-lookup"><span data-stu-id="7bdf9-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="7bdf9-224">tıkla</span><span class="sxs-lookup"><span data-stu-id="7bdf9-224">react</span></span>      | <span data-ttu-id="7bdf9-225">Þ</span><span class="sxs-lookup"><span data-stu-id="7bdf9-225">[C#]</span></span>     | <span data-ttu-id="7bdf9-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="7bdf9-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="7bdf9-227">ASP.NET Core, yanıt verme. js ve Redux ile MVC</span><span class="sxs-lookup"><span data-stu-id="7bdf9-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="7bdf9-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="7bdf9-228">reactredux</span></span> | <span data-ttu-id="7bdf9-229">Þ</span><span class="sxs-lookup"><span data-stu-id="7bdf9-229">[C#]</span></span>     | <span data-ttu-id="7bdf9-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="7bdf9-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="7bdf9-231">SPA şablonlarından birini kullanarak yeni bir proje oluşturmak için, [DotNet New](/dotnet/core/tools/dotnet-new) komutuna şablonun **kısa adını** ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="7bdf9-232">Aşağıdaki komut, sunucu tarafı için yapılandırılmış ASP.NET Core MVC ile bir angular uygulaması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="7bdf9-233">Çalışma zamanı yapılandırma modunu ayarla</span><span class="sxs-lookup"><span data-stu-id="7bdf9-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="7bdf9-234">İki birincil çalışma zamanı yapılandırma modu var:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="7bdf9-235">**Geliştirme**:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-235">**Development**:</span></span>
  * <span data-ttu-id="7bdf9-236">Hata ayıklamayı kolaylaştırmak için kaynak haritaları içerir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="7bdf9-237">, Performans için istemci tarafı kodunu iyileştirmez.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="7bdf9-238">**Üretim**:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-238">**Production**:</span></span>
  * <span data-ttu-id="7bdf9-239">Kaynak eşlemelerini dışlar.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-239">Excludes source maps.</span></span>
  * <span data-ttu-id="7bdf9-240">Paketleme ve küçültmeye göre istemci tarafı kodunu iyileştirir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="7bdf9-241">ASP.NET Core, yapılandırma modunu depolamak için `ASPNETCORE_ENVIRONMENT` adlı bir ortam değişkenini kullanır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="7bdf9-242">Daha fazla bilgi için bkz. [ortamı ayarlama](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="7bdf9-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="7bdf9-243">.NET Core CLI ile Çalıştır</span><span class="sxs-lookup"><span data-stu-id="7bdf9-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="7bdf9-244">Proje kökünde aşağıdaki komutu çalıştırarak gerekli NuGet ve NPM paketlerini geri yükleyin:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="7bdf9-245">Uygulamayı derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="7bdf9-246">Uygulama, [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost üzerinde başlatılır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="7bdf9-247">`http://localhost:5000` Tarayıcıda gezinmek giriş sayfasını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="7bdf9-248">Visual Studio 2017 ile çalıştırma</span><span class="sxs-lookup"><span data-stu-id="7bdf9-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="7bdf9-249">[DotNet New](/dotnet/core/tools/dotnet-new) komutu tarafından oluşturulan *. csproj* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="7bdf9-250">Gerekli NuGet ve NPM paketleri proje açık olduğunda otomatik olarak geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="7bdf9-251">Bu geri yükleme işlemi birkaç dakika sürebilir ve bu işlem tamamlandığında uygulama çalıştırılmaya başlayabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="7bdf9-252">Yeşil çalışma düğmesine veya tuşuna basın `Ctrl + F5`ve tarayıcı uygulamanın giriş sayfasında açılır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="7bdf9-253">Uygulama, [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="7bdf9-254">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="7bdf9-254">Test the app</span></span>

<span data-ttu-id="7bdf9-255">SpaServices şablonları, [karma](https://karma-runner.github.io/1.0/index.html) ve [Jasmine](https://jasmine.github.io/)kullanarak istemci tarafı testleri çalıştıracak şekilde önceden yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="7bdf9-256">Jasmine JavaScript için popüler bir birim testi çerçevesidir, ancak karma bu testler için bir Test Çalıştırıcısı olur.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="7bdf9-257">Karma, geliştiricilerin her değişiklik yapıldığında testi durdurmak ve çalıştırmak için gerekli olmaması gibi [WebPack dev ara yazılımı](#webpack-dev-middleware) ile birlikte çalışmak üzere yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="7bdf9-258">Test çalışmasına veya test çalışmasının kendisine karşı çalışan kod olup olmadığı, test otomatik olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="7bdf9-259">Angular uygulamasını örnek olarak kullanarak, `CounterComponent` *Counter. Component. spec. TS* dosyasında Için iki Jasmine test çalışması zaten sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="7bdf9-260">*Clientapp* dizininde komut istemi ' ni açın.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="7bdf9-261">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="7bdf9-262">Betik, *karma. conf. js* dosyasında tanımlanan ayarları okuyan karma Test Çalıştırıcısı 'nı başlatır.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="7bdf9-263">Diğer ayarların yanı sıra, *karma. conf. js* kendi `files` dizisi aracılığıyla yürütülecek test dosyalarını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="7bdf9-264">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="7bdf9-264">Publish the app</span></span>

<span data-ttu-id="7bdf9-265">Azure 'da yayımlama hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/12474) bakın.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="7bdf9-266">Oluşturulan istemci tarafı varlıkları ve yayımlanan ASP.NET Core yapıtları, dağıtım için hazırlamış bir pakette birleştirmek çok daha fazla olabilir.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="7bdf9-267">Ktam, Spaservice, yayın sürecinin tamamını şu adlı `RunWebpack`özel bir MSBuild hedefi ile düzenler:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="7bdf9-268">MSBuild hedefi aşağıdaki sorumluluklara sahiptir:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="7bdf9-269">NPM paketlerini geri yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="7bdf9-270">Üçüncü taraf, istemci tarafı varlıkların üretim sınıfı derlemesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="7bdf9-271">Özel istemci tarafı varlıkların üretim sınıfı derlemesini oluşturma.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="7bdf9-272">Web paketi tarafından oluşturulan varlıkları Yayımla klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="7bdf9-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="7bdf9-273">Şu çalıştırılırken MSBuild hedefi çağrılır:</span><span class="sxs-lookup"><span data-stu-id="7bdf9-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="7bdf9-274">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7bdf9-274">Additional resources</span></span>

* [<span data-ttu-id="7bdf9-275">Angular belgeleri</span><span class="sxs-lookup"><span data-stu-id="7bdf9-275">Angular Docs</span></span>](https://angular.io/docs)
