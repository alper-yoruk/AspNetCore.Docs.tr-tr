---
title: ASP.NET Core barındırma ve dağıtmaBlazor
author: guardrex
description: Uygulamaları nasıl barındırılacağını ve dağıtacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 482b066d347c2c572b0ffb78ccab1ac391195823
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452245"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="3f377-103">ASP.NET Core barındırma ve dağıtmaBlazor</span><span class="sxs-lookup"><span data-stu-id="3f377-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="3f377-104">, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="3f377-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="3f377-105">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="3f377-105">Publish the app</span></span>

<span data-ttu-id="3f377-106">Uygulamalar yayın yapılandırmasında dağıtım için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="3f377-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f377-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f377-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3f377-108">Gezinti çubuğundan **Build**  >  **Publish {APPLICATION}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3f377-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="3f377-109">*Yayımla hedefini*seçin.</span><span class="sxs-lookup"><span data-stu-id="3f377-109">Select the *publish target*.</span></span> <span data-ttu-id="3f377-110">Yerel olarak yayımlamak için **klasör**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="3f377-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="3f377-111">**Klasör seçin** alanında varsayılan konumu kabul edin veya farklı bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="3f377-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="3f377-112">**Yayımla** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3f377-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3f377-113">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f377-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3f377-114">**Derleme**  >  **yayımlama klasörü**' nü seçin.</span><span class="sxs-lookup"><span data-stu-id="3f377-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="3f377-115">Yayınlanan varlıkların alınacağı klasörü onaylayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="3f377-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3f377-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3f377-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f377-117">Uygulamayı bir sürüm yapılandırmasıyla yayımlamak için [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="3f377-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="3f377-118">Uygulamanın yayımlanması, projenin bağımlılıklarını [geri yüklemeyi](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="3f377-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="3f377-119">Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan Yöntemler ve derlemeler kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="3f377-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="3f377-120">Yayımlama konumları:</span><span class="sxs-lookup"><span data-stu-id="3f377-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="3f377-121">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3f377-121"> WebAssembly</span></span>
  * <span data-ttu-id="3f377-122">Tek başına: uygulama */BIN/Release/{Target Framework}/Publish/Wwwroot* klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="3f377-122">Standalone: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="3f377-123">Uygulamayı statik bir site olarak dağıtmak için *Wwwroot* klasörünün içeriğini statik site konağına kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="3f377-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="3f377-124">Barındırılan: istemci Blazor webassembly uygulaması sunucu uygulamasının */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="3f377-124">Hosted: The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="3f377-125">*Yayımla* klasörünün içeriğini konağa dağıtın.</span><span class="sxs-lookup"><span data-stu-id="3f377-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="3f377-126">Sunucu: uygulama */BIN/Release/{Target Framework}/Publish* klasörüne yayımlandı.</span><span class="sxs-lookup"><span data-stu-id="3f377-126"> Server: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="3f377-127">*Yayımla* klasörünün içeriğini konağa dağıtın.</span><span class="sxs-lookup"><span data-stu-id="3f377-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="3f377-128">Klasördeki varlıklar Web sunucusuna dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="3f377-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="3f377-129">Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir süreç olabilir.</span><span class="sxs-lookup"><span data-stu-id="3f377-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="3f377-130">Uygulama temel yolu</span><span class="sxs-lookup"><span data-stu-id="3f377-130">App base path</span></span>

<span data-ttu-id="3f377-131">*Uygulama temel yolu* , UYGULAMANıN kök URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="3f377-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="3f377-132">Aşağıdaki ASP.NET Core uygulamayı ve alt uygulamayı göz önünde bulundurun Blazor :</span><span class="sxs-lookup"><span data-stu-id="3f377-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="3f377-133">ASP.NET Core uygulaması şu şekilde adlandırılır `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="3f377-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="3f377-134">Uygulama fiziksel olarak *d:/MyApp*konumunda bulunur.</span><span class="sxs-lookup"><span data-stu-id="3f377-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="3f377-135">İstekleri tarihinde alınır `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="3f377-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="3f377-136">BlazorAdlı bir uygulama `CoolApp` , öğesinin bir alt uygulamasıdır `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="3f377-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="3f377-137">Alt uygulama fiziksel olarak *d:/MyApp/CoolApp*konumunda bulunur.</span><span class="sxs-lookup"><span data-stu-id="3f377-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="3f377-138">İstekleri tarihinde alınır `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="3f377-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="3f377-139">İçin ek yapılandırma belirtmeden `CoolApp` , Bu senaryodaki alt uygulama, sunucuda nerede bulunduğu konusunda bilgi sahibi değildir.</span><span class="sxs-lookup"><span data-stu-id="3f377-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="3f377-140">Örneğin, uygulama ilgili URL yolunda bulunduğunu bilmeden kaynaklarına doğru göreli URL 'Ler oluşturamıyoruz `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="3f377-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="3f377-141">Uygulamanın temel yolu için yapılandırma sağlamak üzere Blazor `https://www.contoso.com/CoolApp/` , `<base>` etiketin `href` özniteliği *Pages/_Host. cshtml* dosyasında ( Blazor sunucu) veya *Wwwroot/index.html* dosyasında ( Blazor webassembly) göreli kök yoluna ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3f377-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="3f377-142">Sunucu uygulamaları Ayrıca, <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> uygulamanın istek ardışık düzeninde çağırarak sunucu tarafı taban yolunu ayarlar `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3f377-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="3f377-143">Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL 'Ler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="3f377-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="3f377-144">Farklı dizin yapısı düzeylerindeki bileşenler, uygulama genelinde konumlardaki diğer kaynakların bağlantılarını oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="3f377-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="3f377-145">Uygulama temel yolu, `href` bağlantının hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri ele almak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3f377-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="3f377-146">BlazorYönlendirici iç gezinmeyi işler.</span><span class="sxs-lookup"><span data-stu-id="3f377-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="3f377-147">Birçok barındırma senaryosunda, uygulamanın göreli URL yolu uygulamanın köküdür.</span><span class="sxs-lookup"><span data-stu-id="3f377-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="3f377-148">Bu durumlarda, uygulamanın göreli URL taban yolu `<base href="/" />` , bir uygulamanın varsayılan yapılandırması olan bir eğik çizgi () olur Blazor .</span><span class="sxs-lookup"><span data-stu-id="3f377-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="3f377-149">GitHub sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu, sunucunun uygulamanın göreli URL 'SI yolu olarak ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3f377-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="3f377-150">Uygulamanın temel yolunu ayarlamak için, `<base>` `<head>` *sayfa/_Host. cshtml* dosyası ( Blazor sunucu) veya *Wwwroot/index.html* dosyasının (webassembly) etiket öğeleri içindeki etiketi güncelleştirin Blazor .</span><span class="sxs-lookup"><span data-stu-id="3f377-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="3f377-151">`href`Öznitelik değerini olarak ayarlayın `/{RELATIVE URL PATH}/` (sondaki eğik çizgi gereklidir), burada `{RELATIVE URL PATH}` UYGULAMANıN tam göreli URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="3f377-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="3f377-152">BlazorKök olmayan GÖRELI URL yoluna (örneğin,) sahip bir webassembly uygulaması için `<base href="/CoolApp/">` , uygulama *yerel olarak çalıştırıldığında*kaynaklarını bulamaz.</span><span class="sxs-lookup"><span data-stu-id="3f377-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="3f377-153">Yerel geliştirme ve test sırasında bu sorunu aşmak için, *path base* `href` `<base>` çalışma zamanında etiketinin değeriyle eşleşen bir yol temel bağımsız değişkeni sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3f377-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="3f377-154">Sondaki eğik çizgi eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="3f377-154">Don't include a trailing slash.</span></span> <span data-ttu-id="3f377-155">Uygulamayı yerel olarak çalıştırırken yol temel bağımsız değişkenini geçirmek için, `dotnet run` komutu uygulamanın dizininden çalıştırın, `--pathbase` seçeneği:</span><span class="sxs-lookup"><span data-stu-id="3f377-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="3f377-156">BlazorGÖRELI URL yolu () olan bir webassembly uygulaması için `/CoolApp/` , `<base href="/CoolApp/">` komut şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="3f377-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="3f377-157">BlazorWebassembly uygulaması tarihinde yerel olarak yanıt verir `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="3f377-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="3f377-158">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="3f377-158">Deployment</span></span>

<span data-ttu-id="3f377-159">Dağıtım Kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="3f377-159">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
