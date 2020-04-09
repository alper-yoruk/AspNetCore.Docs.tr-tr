---
title: ASP.NET Core'u barındırma ve dağıtmaBlazor
author: guardrex
description: Uygulamaları nasıl barındırıp Blazor dağıtılayın keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434271"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="2e926-103">Core BlazorASP.NET ana bilgisayar ve dağıtım</span><span class="sxs-lookup"><span data-stu-id="2e926-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="2e926-104">Luke [Latham](https://github.com/guardrex)tarafından , [Rainer Stropek](https://www.timecockpit.com), ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2e926-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="2e926-105">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="2e926-105">Publish the app</span></span>

<span data-ttu-id="2e926-106">Uygulamalar Sürüm yapılandırmasında dağıtım için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="2e926-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e926-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e926-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2e926-108">Gezinti çubuğundan **Yapı** > **Yayımla {APPLICATION}** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="2e926-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="2e926-109">*Yayımlama hedefini*seçin.</span><span class="sxs-lookup"><span data-stu-id="2e926-109">Select the *publish target*.</span></span> <span data-ttu-id="2e926-110">Yerel olarak yayımlamak için **Klasör'ü**seçin.</span><span class="sxs-lookup"><span data-stu-id="2e926-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="2e926-111">Klasör alanı **seç'teki** varsayılan konumu kabul edin veya farklı bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="2e926-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="2e926-112">**Yayımla** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2e926-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2e926-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2e926-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2e926-114">Uygulamayı Yayın yapılandırmasıyla yayınlamak için [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="2e926-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="2e926-115">Uygulamayı yayımlama, projenin bağımlılıklarının [geri yüklenmesini](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur.](/dotnet/core/tools/dotnet-build)</span><span class="sxs-lookup"><span data-stu-id="2e926-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="2e926-116">Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan yöntemler ve derlemeler kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="2e926-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="2e926-117">Konumları yayımla:</span><span class="sxs-lookup"><span data-stu-id="2e926-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="2e926-118">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2e926-118"> WebAssembly</span></span>
  * <span data-ttu-id="2e926-119">Bağımsız &ndash; Uygulama */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="2e926-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="2e926-120">Uygulamayı statik bir site olarak dağıtmak *için, wwwroot* klasörünün içeriğini statik site ana bilgisayarına kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="2e926-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="2e926-121">&ndash; Barındırılan Blazor istemci WebAssembly uygulaması sunucu uygulamasının */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe, sunucu uygulamasının diğer statik web varlıklarıyla birlikte yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="2e926-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="2e926-122">*Yayımlama* klasörünün içeriğini ana bilgisayara dağıtın.</span><span class="sxs-lookup"><span data-stu-id="2e926-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="2e926-123">Server &ndash; Uygulama */bin/Release/{TARGET FRAMEWORK}/publish* klasöründe yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="2e926-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="2e926-124">*Yayımlama* klasörünün içeriğini ana bilgisayara dağıtın.</span><span class="sxs-lookup"><span data-stu-id="2e926-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="2e926-125">Klasördeki varlıklar web sunucusuna dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="2e926-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="2e926-126">Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir işlem olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e926-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="2e926-127">Uygulama tabanı yolu</span><span class="sxs-lookup"><span data-stu-id="2e926-127">App base path</span></span>

<span data-ttu-id="2e926-128">*Uygulama temel yolu,* uygulamanın kök URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="2e926-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="2e926-129">Aşağıdaki ASP.NET Core uygulamasını Blazor ve alt uygulamalarını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2e926-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="2e926-130">ASP.NET Core uygulamasının `MyApp`adı:</span><span class="sxs-lookup"><span data-stu-id="2e926-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="2e926-131">Uygulama fiziksel olarak *d:/MyApp*adresinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="2e926-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="2e926-132">İstekler `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="2e926-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="2e926-133">Adlı Blazor `CoolApp` bir uygulama bir alt `MyApp`uygulamadır:</span><span class="sxs-lookup"><span data-stu-id="2e926-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="2e926-134">Alt uygulama fiziksel olarak *d:/MyApp/CoolApp*adresinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="2e926-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="2e926-135">İstekler `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="2e926-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="2e926-136">Için `CoolApp`ek yapılandırma belirtmeden, bu senaryoda alt uygulama sunucuda nerede bulunduğu hakkında hiçbir bilgiye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2e926-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="2e926-137">Örneğin, uygulama, göreli URL yolunda `/CoolApp/`bulunduğunu bilmeden kaynaklarına doğru göreli URL'ler oluşturamaz.</span><span class="sxs-lookup"><span data-stu-id="2e926-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="2e926-138">Uygulamanın Blazor temel yolu için yapılandırma `https://www.contoso.com/CoolApp/`sağlamak `<base>` için, `href` etiketin özniteliği *Sayfalar/_Host.cshtml* dosyasındaki (Sunucu)Blazor veya *wwwroot/index.html* dosyasındakiBlazor (WebAssembly) göreli kök yoluna ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="2e926-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="2e926-139">Sunucu uygulamaları ayrıca uygulamanın istek ardışık <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> hattını arayarak sunucu `Startup.Configure`tarafındaki temel yolu ayarlar:</span><span class="sxs-lookup"><span data-stu-id="2e926-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="2e926-140">Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL'ler oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e926-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="2e926-141">Dizin yapısının farklı düzeylerindeki bileşenler, uygulama nın dışındaki konumlarda diğer kaynaklara bağlantılar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="2e926-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="2e926-142">Uygulama temel yolu, bağlantının `href` hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri kesmek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e926-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="2e926-143">Blazor Yönlendirici dahili gezinmeyi işler.</span><span class="sxs-lookup"><span data-stu-id="2e926-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="2e926-144">Birçok barındırma senaryosunda, uygulamaya giden göreli URL yolu uygulamanın köküdür.</span><span class="sxs-lookup"><span data-stu-id="2e926-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="2e926-145">Bu gibi durumlarda, uygulamanın göreli URL taban`<base href="/" />`yolu, bir Blazor uygulama için varsayılan yapılandırma olan bir ileri eğik çizgidir.</span><span class="sxs-lookup"><span data-stu-id="2e926-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="2e926-146">GitHub Sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu sunucunun uygulamanın göreli URL yoluna ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e926-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="2e926-147">Uygulamanın temel yolunu ayarlamak için, `<base>` *Sayfalar/_Host.cshtml* dosyasının (Sunucu)Blazor veya *wwwroot/index.html* dosyasınınBlazor (WebAssembly) `<head>` etiket öğeleri içindeki etiketi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2e926-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="2e926-148">Öznitelik `href` değerini `/{RELATIVE URL PATH}/` uygulamanın tam göreli URL `{RELATIVE URL PATH}` yolunun olduğu yere (sondaki eğik çizgi gereklidir) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2e926-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="2e926-149">Kök Blazor olmayan göreli URL yolu olan bir WebAssembly `<base href="/CoolApp/">`uygulaması için (örneğin,), uygulama *yerel olarak çalıştırıldığında*kaynaklarını bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="2e926-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="2e926-150">Yerel geliştirme ve sınama sırasında bu sorunun üstesinden gelmek `href` için, `<base>` çalışma zamanında etiketin değeriyle eşleşen bir *yol temel* bağımsız değişkeni sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e926-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="2e926-151">Bir çizgi eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="2e926-151">Don't include a trailing slash.</span></span> <span data-ttu-id="2e926-152">Uygulamayı yerel olarak çalıştırırken yol temel bağımsız `dotnet run` değişkenini geçmek için, `--pathbase` uygulamanın dizininden komutu aşağıdaki seçenekle uygulayın:</span><span class="sxs-lookup"><span data-stu-id="2e926-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="2e926-153">Göreceli Blazor URL yolu `/CoolApp/` olan bir WebAssembly`<base href="/CoolApp/">`uygulaması için ( ), komutu:</span><span class="sxs-lookup"><span data-stu-id="2e926-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="2e926-154">Blazor WebAssembly uygulaması yerel olarak `http://localhost:port/CoolApp`yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="2e926-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="2e926-155">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="2e926-155">Deployment</span></span>

<span data-ttu-id="2e926-156">Dağıtım kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="2e926-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
