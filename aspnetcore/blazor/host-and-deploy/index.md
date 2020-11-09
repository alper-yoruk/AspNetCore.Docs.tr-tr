---
title: 'ASP.NET Core barındırma ve dağıtma Blazor'
author: guardrex
description: 'Uygulamaları nasıl barındırılacağını ve dağıtacağınızı öğrenin Blazor .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 082072d2b70abfe60da8e2cd40daa8b93ebcc9ac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055821"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a><span data-ttu-id="1fe6f-103">ASP.NET Core barındırma ve dağıtma Blazor</span><span class="sxs-lookup"><span data-stu-id="1fe6f-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="1fe6f-104">, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="1fe6f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="1fe6f-105">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="1fe6f-105">Publish the app</span></span>

<span data-ttu-id="1fe6f-106">Uygulamalar yayın yapılandırmasında dağıtım için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fe6f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe6f-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1fe6f-108">Gezinti çubuğundan **Build**  >  **Publish {APPLICATION}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="1fe6f-109">*Yayımla hedefini* seçin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-109">Select the *publish target* .</span></span> <span data-ttu-id="1fe6f-110">Yerel olarak yayımlamak için **klasör** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-110">To publish locally, select **Folder** .</span></span>
1. <span data-ttu-id="1fe6f-111">**Klasör seçin** alanında varsayılan konumu kabul edin veya farklı bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="1fe6f-112">**`Publish`** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fe6f-113">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe6f-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1fe6f-114">**Derleme**  >  **yayımlama klasörü** ' nü seçin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-114">Select **Build** > **Publish to Folder** .</span></span>
1. <span data-ttu-id="1fe6f-115">Yayınlanan varlıkların alınacağı klasörü onaylayın ve öğesini seçin **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-115">Confirm the folder to receive the published assets and select **`Publish`** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fe6f-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1fe6f-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1fe6f-117">[`dotnet publish`](/dotnet/core/tools/dotnet-publish)Uygulamayı bir sürüm yapılandırmasıyla yayımlamak için komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="1fe6f-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="1fe6f-118">Uygulamanın yayımlanması, projenin bağımlılıklarını [geri yüklemeyi](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="1fe6f-119">Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan Yöntemler ve derlemeler kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="1fe6f-120">Yayımlama konumları:</span><span class="sxs-lookup"><span data-stu-id="1fe6f-120">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="1fe6f-121">Tek başına: uygulama `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` klasöre yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="1fe6f-122">Uygulamayı statik bir site olarak dağıtmak için, `wwwroot` klasörün içeriğini statik site konağına kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="1fe6f-123">Barındırılan: istemci uygulama, sunucu uygulamasının Blazor WebAssembly `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` diğer statik Web varlıklarıyla birlikte sunucu uygulamasının klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-123">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="1fe6f-124">`publish`Klasörün içeriğini konağa dağıtın.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-124">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="1fe6f-125">Blazor Server: Uygulama `/bin/Release/{TARGET FRAMEWORK}/publish` klasöründe yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-125">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="1fe6f-126">`publish`Klasörün içeriğini konağa dağıtın.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="1fe6f-127">Klasördeki varlıklar Web sunucusuna dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="1fe6f-128">Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir süreç olabilir.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="1fe6f-129">Uygulama temel yolu</span><span class="sxs-lookup"><span data-stu-id="1fe6f-129">App base path</span></span>

<span data-ttu-id="1fe6f-130">*Uygulama temel yolu* , UYGULAMANıN kök URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="1fe6f-131">Aşağıdaki ASP.NET Core uygulamayı ve alt uygulamayı göz önünde bulundurun Blazor :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-131">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="1fe6f-132">ASP.NET Core uygulaması şu şekilde adlandırılır `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="1fe6f-133">Uygulama fiziksel olarak konumunda bulunur `d:/MyApp` .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="1fe6f-134">İstekleri tarihinde alınır `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="1fe6f-135">BlazorAdlı bir uygulama `CoolApp` , öğesinin bir alt uygulamasıdır `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-135">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="1fe6f-136">Alt uygulama fiziksel olarak konumunda bulunur `d:/MyApp/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="1fe6f-137">İstekleri tarihinde alınır `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="1fe6f-138">İçin ek yapılandırma belirtmeden `CoolApp` , Bu senaryodaki alt uygulama, sunucuda nerede bulunduğu konusunda bilgi sahibi değildir.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="1fe6f-139">Örneğin, uygulama ilgili URL yolunda bulunduğunu bilmeden kaynaklarına doğru göreli URL 'Ler oluşturamıyoruz `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="1fe6f-140">Uygulamanın temel yolu için yapılandırma sağlamak üzere Blazor `https://www.contoso.com/CoolApp/` `<base>` etiketinin `href` özniteliği `Pages/_Host.cshtml` dosyadaki ( Blazor Server ) veya `wwwroot/index.html` dosyadaki () göreli kök yoluna ayarlanır Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-140">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="1fe6f-141">Blazor Server uygulamalar Ayrıca <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> uygulamanın istek ardışık düzeninde çağırarak sunucu tarafı taban yolunu ayarlar `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-141">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="1fe6f-142">Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL 'Ler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-142">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="1fe6f-143">Farklı dizin yapısı düzeylerindeki bileşenler, uygulama genelinde konumlardaki diğer kaynakların bağlantılarını oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-143">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="1fe6f-144">Uygulama temel yolu, `href` bağlantının hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri ele almak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-144">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="1fe6f-145">BlazorYönlendirici iç gezinmeyi işler.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-145">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="1fe6f-146">Birçok barındırma senaryosunda, uygulamanın göreli URL yolu uygulamanın köküdür.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-146">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="1fe6f-147">Bu durumlarda, uygulamanın göreli URL taban yolu `<base href="/" />` , bir uygulamanın varsayılan yapılandırması olan bir eğik çizgi () olur Blazor .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-147">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="1fe6f-148">GitHub sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu, sunucunun uygulamanın göreli URL 'SI yolu olarak ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-148">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="1fe6f-149">Uygulamanın temel yolunu ayarlamak için `<base>` `<head>` `Pages/_Host.cshtml` dosyanın ( Blazor Server ) veya `wwwroot/index.html` dosyanın () etiket öğeleri içindeki etiketi güncelleştirin Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-149">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="1fe6f-150">`href`Öznitelik değerini olarak ayarlayın `/{RELATIVE URL PATH}/` (sondaki eğik çizgi gereklidir), burada `{RELATIVE URL PATH}` UYGULAMANıN tam göreli URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-150">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="1fe6f-151">Blazor WebAssemblyKök olmayan GÖRELI URL yoluna (örneğin,) sahip bir uygulama için `<base href="/CoolApp/">` , uygulama *yerel olarak çalıştırıldığında* kaynaklarını bulamaz.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-151">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally* .</span></span> <span data-ttu-id="1fe6f-152">Yerel geliştirme ve test sırasında bu sorunu aşmak için, *path base* `href` `<base>` çalışma zamanında etiketinin değeriyle eşleşen bir yol temel bağımsız değişkeni sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-152">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="1fe6f-153">Sondaki eğik çizgi eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-153">Don't include a trailing slash.</span></span> <span data-ttu-id="1fe6f-154">Uygulamayı yerel olarak çalıştırırken yol temel bağımsız değişkenini geçirmek için, `dotnet run` komutu uygulamanın dizininden çalıştırın, `--pathbase` seçeneği:</span><span class="sxs-lookup"><span data-stu-id="1fe6f-154">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="1fe6f-155">Blazor WebAssemblyGÖRELI URL yolu () olan bir uygulama için `/CoolApp/` , `<base href="/CoolApp/">` komut şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="1fe6f-155">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="1fe6f-156">Blazor WebAssemblyUygulama üzerinde yerel olarak yanıt verir `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="1fe6f-156">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="1fe6f-157">**Blazor Server`MapFallbackToPage`yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="1fe6f-157">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="1fe6f-158">Aşağıdaki yolu <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> içine geçirin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-158">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="1fe6f-159">Yer tutucu, `{RELATIVE PATH}` sunucuda kök olmayan yoldur.</span><span class="sxs-lookup"><span data-stu-id="1fe6f-159">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="1fe6f-160">Örneğin, `CoolApp` uygulamanın kök olmayan URL 'si ise yer tutucu segmentdir `https://{HOST}:{PORT}/CoolApp/` :</span><span class="sxs-lookup"><span data-stu-id="1fe6f-160">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="1fe6f-161">**Birden çok Blazor WebAssembly uygulamayı barındırma**</span><span class="sxs-lookup"><span data-stu-id="1fe6f-161">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="1fe6f-162">Barındırılan bir çözümde birden çok uygulamayı barındırma hakkında daha fazla bilgi için Blazor WebAssembly Blazor bkz <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> ..</span><span class="sxs-lookup"><span data-stu-id="1fe6f-162">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="1fe6f-163">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="1fe6f-163">Deployment</span></span>

<span data-ttu-id="1fe6f-164">Dağıtım Kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="1fe6f-164">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
