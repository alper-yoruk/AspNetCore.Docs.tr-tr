---
title: TypeScript ve SignalR WebPack ile ASP.NET Core kullanma
author: ssougnez
description: Bu öğreticide, Web paketini istemcisi TypeScript 'te yazılmış bir ASP.NET Core SignalR Web uygulaması paketleyip derlemek üzere yapılandırırsınız.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 67a6217055db69fe540412f42411dd3a33bbbe73
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775511"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="96d6b-103">TypeScript ve WebPack ile ASP.NET Core SignalR kullanın</span><span class="sxs-lookup"><span data-stu-id="96d6b-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="96d6b-104">, [Sébastien Sougnez](https://twitter.com/ssougnez) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="96d6b-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="96d6b-105">[WebPack](https://webpack.js.org/) , geliştiricilerin bir Web uygulamasının istemci tarafı kaynaklarını paketleyip oluşturmalarına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="96d6b-106">Bu öğreticide, istemcisinin [TypeScript](https://www.typescriptlang.org/)'te yazıldığı bir ASP.NET Core SignalR Web uygulamasında WebPack 'in kullanımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="96d6b-107">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="96d6b-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="96d6b-108">Bir başlatıcı ASP.NET Core SignalR uygulaması yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="96d6b-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="96d6b-109">SignalR TypeScript istemcisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="96d6b-110">WebPack kullanarak derleme işlem hattı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="96d6b-111">SignalR sunucusunu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="96d6b-112">İstemci ve sunucu arasındaki iletişimi etkinleştir</span><span class="sxs-lookup"><span data-stu-id="96d6b-112">Enable communication between client and server</span></span>

<span data-ttu-id="96d6b-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="96d6b-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="96d6b-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="96d6b-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96d6b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d6b-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="96d6b-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="96d6b-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="96d6b-117">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="96d6b-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="96d6b-118">[NPM](https://www.npmjs.com/) ile [Node. js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="96d6b-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96d6b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="96d6b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="96d6b-121">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="96d6b-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="96d6b-122">Visual Studio Code Version 1.17.1 veya üzeri için C#</span><span class="sxs-lookup"><span data-stu-id="96d6b-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="96d6b-123">[NPM](https://www.npmjs.com/) ile [Node. js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="96d6b-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="96d6b-124">ASP.NET Core Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="96d6b-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96d6b-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d6b-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96d6b-126">Visual Studio 'Yu, *Path* ortam değişkeninde NPM için arama yapmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="96d6b-127">Varsayılan olarak, Visual Studio yükleme dizininde bulunan NPM sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="96d6b-128">Visual Studio 'da şu yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="96d6b-129">Visual Studio’yu başlatın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-129">Launch Visual Studio.</span></span> <span data-ttu-id="96d6b-130">Başlangıç penceresinde, **kod olmadan devam et**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="96d6b-131">**Araçlar** > **Options** Seçenekler > **Projeler ve çözümler** > **Web paket yönetimi** > **dış Web araçları**' na gidin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="96d6b-132">Listeden *$ (yol)* girişini seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="96d6b-133">Yukarı oka tıklayarak girişi listedeki ikinci konuma taşıyın ve **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Visual Studio yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="96d6b-135">Visual Studio yapılandırması tamamlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="96d6b-136">**Dosya** > **New**yeni > **Proje** menü seçeneğini kullanın ve **ASP.NET Core Web uygulaması** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="96d6b-137">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-137">Select **Next**.</span></span>
1. <span data-ttu-id="96d6b-138">Projeyi *Signalrwebpack*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="96d6b-139">Hedef çerçeve açılır listesinden *.NET Core* ' u seçin ve çerçeve Seçicisi açılır listesinden *ASP.NET Core 3,1* ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="96d6b-140">**Boş** şablonu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="96d6b-141">`Microsoft.TypeScript.MSBuild` Paketi projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="96d6b-142">**Çözüm Gezgini** (sağ bölme) içinde, proje düğümüne sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="96d6b-143">Araştır sekmesine **gidin** ve ardından paketi yüklemek `Microsoft.TypeScript.MSBuild`için sağ tarafta bulunan ara ' **yı tıklatın.**</span><span class="sxs-lookup"><span data-stu-id="96d6b-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="96d6b-144">Visual Studio, NuGet paketini **Çözüm Gezgini**' deki **Bağımlılıklar** düğümüne ekler ve projede TypeScript derlemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96d6b-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="96d6b-146">**Tümleşik terminalde**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="96d6b-147">Komut `dotnet new` , bir *Signalrwebpack* dizininde boş bir ASP.NET Core Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="96d6b-148">`code` Komutu, Visual Studio Code geçerli örneğindeki *Signalrwebpack* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="96d6b-149">**Tümleşik terminalde**aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="96d6b-150">Yukarıdaki komut, [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) paketini ekler ve projede TypeScript derlemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="96d6b-151">WebPack ve TypeScript yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="96d6b-152">Aşağıdaki adımlar, TypeScript 'in JavaScript 'e dönüştürülmesini ve istemci tarafı kaynaklarını paketlemeyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="96d6b-153">Bir *Package. JSON* dosyası oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="96d6b-154">Vurgulanan özelliği *Package. JSON* dosyasına ekleyin ve dosya değişikliklerini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="96d6b-155">`private` Özelliği, bir sonraki `true` adımda paket yükleme uyarılarını engelleyecek şekilde ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="96d6b-156">Gerekli NPM paketlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-156">Install the required npm packages.</span></span> <span data-ttu-id="96d6b-157">Proje kökünden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="96d6b-158">Aklınızda bazı komut ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="96d6b-158">Some command details to note:</span></span>

    * <span data-ttu-id="96d6b-159">Sürüm numarası her paket adı `@` için işareti izler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="96d6b-160">NPM bu özel paket sürümlerini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="96d6b-161">Seçeneği `-E` , NPM 'nin [semantik sürüm](https://semver.org/) aralığı işleçlerini *Package. JSON*öğesine yazmanın varsayılan davranışını devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="96d6b-162">Örneğin, `"webpack": "4.41.5"` yerine kullanılır `"webpack": "^4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="96d6b-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="96d6b-163">Bu seçenek, daha yeni paket sürümlerine istenmeden yükseltme yapılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="96d6b-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="96d6b-164">Daha fazla ayrıntı için bkz. [NPM-Install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="96d6b-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="96d6b-165">`scripts` *Package. JSON* dosyasının özelliğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="96d6b-166">Betiklerin bazı açıklamaları:</span><span class="sxs-lookup"><span data-stu-id="96d6b-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="96d6b-167">`build`: İstemci tarafı kaynaklarını geliştirme modunda paketler ve dosya değişikliklerini izler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="96d6b-168">Dosya izleyici, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="96d6b-169">`mode` Seçeneği, Tree gerçekleşmesi ve minbirleşme gibi üretim iyileştirmeleri devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="96d6b-170">Yalnızca geliştirme `build` aşamasında kullanın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="96d6b-171">`release`: İstemci tarafı kaynaklarını üretim modunda paketlayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="96d6b-172">`publish`: İstemci tarafı `release` kaynaklarını üretim modunda paketleyip betiği çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="96d6b-173">Uygulamayı yayımlamak için .NET Core CLI [Publish](/dotnet/core/tools/dotnet-publish) komutunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="96d6b-174">Aşağıdaki kodla, proje kökünde *WebPack. config. js*adlı bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="96d6b-175">Yukarıdaki dosya Web paketi derlemesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="96d6b-176">Aklınızda bazı yapılandırma ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="96d6b-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="96d6b-177">`output` Özelliği, *dağ*'ın varsayılan değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="96d6b-178">Paket, *Wwwroot* dizininde yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="96d6b-179">Dizi `resolve.extensions` , SignalR Istemci JavaScript 'i içeri aktarmak için *. js* içerir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="96d6b-180">Projenin istemci tarafı varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="96d6b-181">Aşağıdaki biçimlendirmeyle *src/index.html* oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="96d6b-182">Önceki HTML, giriş sayfasının ortak işaretlemesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="96d6b-183">Yeni bir *src/CSS* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="96d6b-184">Amacı, projenin *. css* dosyalarını depolamadır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="96d6b-185">Şu CSS ile *src/CSS/Main. css* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="96d6b-186">Önceki *Main. css* dosyası uygulamayı stiller.</span><span class="sxs-lookup"><span data-stu-id="96d6b-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="96d6b-187">Şu JSON ile *src/tsconfig. JSON* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="96d6b-188">Yukarıdaki kod, TypeScript derleyicisini [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="96d6b-189">Aşağıdaki kodla *src/index. TS* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="96d6b-190">Önceki TypeScript, DOM öğelerine başvuruları alır ve iki olay işleyicisini ekler:</span><span class="sxs-lookup"><span data-stu-id="96d6b-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="96d6b-191">`keyup`: Bu olay Kullanıcı `tbMessage`metin kutusuna yazdığında ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="96d6b-192">Kullanıcı `send` **ENTER** tuşuna bastığında işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="96d6b-193">`click`: Kullanıcı **Gönder** düğmesine tıkladığında bu olay ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="96d6b-194">`send` işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="96d6b-195">Uygulamayı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-195">Configure the app</span></span>

1. <span data-ttu-id="96d6b-196">İçinde `Startup.Configure`, [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [usestaticfiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)öğesine çağrılar ekleyin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="96d6b-197">Yukarıdaki kod, sunucunun *index. html* dosyasını bulmasını ve sunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="96d6b-198">Dosya, kullanıcının tam URL 'sini veya Web uygulamasının kök URL 'sini girip girmediğini görür.</span><span class="sxs-lookup"><span data-stu-id="96d6b-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="96d6b-199">Sonunda `Startup.Configure`, bir */hub* yolunu `ChatHub` hub 'a eşleyin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="96d6b-200">Merhaba Dünya görüntülenen kodu değiştirin *!*</span><span class="sxs-lookup"><span data-stu-id="96d6b-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="96d6b-201">Aşağıdaki satırla:</span><span class="sxs-lookup"><span data-stu-id="96d6b-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="96d6b-202">İçinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="96d6b-203">SignalR hub 'ını depolamak için proje kök *Signalrwebpack/* içindeki *hub* adlı yeni bir dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="96d6b-204">Aşağıdaki kodla hub *hub 'ları/ChatHub. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="96d6b-205">Başvuruyu çözümlemek için `using` Startup.cs dosyasının en üstüne aşağıdaki ifadeyi ekleyin: *Startup.cs* `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="96d6b-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="96d6b-206">İstemci ve sunucu iletişimini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="96d6b-206">Enable client and server communication</span></span>

<span data-ttu-id="96d6b-207">Uygulama Şu anda ileti göndermek için temel bir form görüntülüyor, ancak henüz işlevsel değil.</span><span class="sxs-lookup"><span data-stu-id="96d6b-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="96d6b-208">Sunucu belirli bir yolu dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="96d6b-209">Proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="96d6b-210">Önceki komut yüklenir:</span><span class="sxs-lookup"><span data-stu-id="96d6b-210">The preceding command installs:</span></span>

     * <span data-ttu-id="96d6b-211">İstemcinin sunucuya ileti göndermesini sağlayan [SignalR TypeScript istemcisi](https://www.npmjs.com/package/@microsoft/signalr).</span><span class="sxs-lookup"><span data-stu-id="96d6b-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="96d6b-212">Node. js türlerinin derleme zamanı denetimini sağlayan Node. js için TypeScript tür tanımları.</span><span class="sxs-lookup"><span data-stu-id="96d6b-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="96d6b-213">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="96d6b-214">Yukarıdaki kod, sunucudan ileti almayı destekler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="96d6b-215">`HubConnectionBuilder` Sınıfı, sunucu bağlantısını yapılandırmak için yeni bir Oluşturucu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="96d6b-216">`withUrl` İşlevi hub URL 'sini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="96d6b-217">SignalR, istemci ile sunucu arasında ileti alışverişi yapılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="96d6b-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="96d6b-218">Her ileti belirli bir ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-218">Each message has a specific name.</span></span> <span data-ttu-id="96d6b-219">Örneğin, adı `messageReceived` olan mesajlar ileti bölgesindeki yeni iletiyi görüntülemeden sorumlu mantığı çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="96d6b-220">Belirli bir iletiyi dinlemek, `on` işlevi aracılığıyla yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="96d6b-221">Herhangi bir sayıda ileti adı ile listenlebilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="96d6b-222">Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye geçirmek da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="96d6b-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="96d6b-223">İstemci bir ileti aldıktan sonra yazarın adı ve onun `div` `innerHTML` özniteliğinde ileti içeriğiyle yeni bir öğe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="96d6b-224">İletileri görüntüleyen Main `div` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="96d6b-225">Artık istemci bir ileti aldığına göre, ileti gönderecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="96d6b-226">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="96d6b-227">WebSockets bağlantısı aracılığıyla bir ileti gönderildiğinde `send` yönteminin çağrılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="96d6b-228">Yöntemin ilk parametresi ileti adıdır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="96d6b-229">İleti verileri diğer parametreleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="96d6b-230">Bu örnekte, sunucusuna gönderildiği şekilde `newMessage` tanımlanan bir ileti.</span><span class="sxs-lookup"><span data-stu-id="96d6b-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="96d6b-231">İleti, bir metin kutusundan Kullanıcı adından ve Kullanıcı girişinden oluşur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="96d6b-232">Gönderme işlemi çalışırsa metin kutusu değeri temizlenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="96d6b-233">`NewMessage` yönetimini `ChatHub` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="96d6b-234">Yukarıdaki kod, sunucu onları aldıktan sonra tüm bağlı kullanıcılara ileti almış iletileri yayınlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="96d6b-235">Tüm iletileri almak için genel `on` bir yöntem olması gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="96d6b-236">İleti adından sonra adlandırılmış bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="96d6b-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="96d6b-237">Bu örnekte, TypeScript istemcisi olarak `newMessage`tanımlanan bir ileti gönderir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="96d6b-238">C# `NewMessage` yöntemi, istemci tarafından gönderilen verileri bekliyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="96d6b-239">Istemcilerde [Sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) çağrısı yapılır [. tümü](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="96d6b-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="96d6b-240">Alınan iletiler, hub 'a bağlı tüm istemcilere gönderilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="96d6b-241">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="96d6b-241">Test the app</span></span>

<span data-ttu-id="96d6b-242">Uygulamanın aşağıdaki adımlarla çalıştığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96d6b-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d6b-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="96d6b-244">Web paketini *yayın* modunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="96d6b-245">**Paket Yöneticisi konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="96d6b-246">Proje kökünde değilseniz, komutu girmeden önce girin `cd SignalRWebPack` .</span><span class="sxs-lookup"><span data-stu-id="96d6b-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="96d6b-247">Hata ayıklayıcıyı eklemeden uygulamayı tarayıcıda başlatmak için hata ayıklama**olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="96d6b-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="96d6b-248">*Wwwroot/index.html* dosyası ' de `http://localhost:<port_number>`sunulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="96d6b-249">Derleme hataları alırsanız, çözümü kapatıp yeniden açmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="96d6b-250">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="96d6b-251">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="96d6b-252">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="96d6b-253">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96d6b-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="96d6b-255">Proje kökünde aşağıdaki komutu yürüterek Web paketini *yayın* modunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="96d6b-256">Proje kökünde aşağıdaki komutu yürüterek uygulamayı derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="96d6b-257">Web sunucusu uygulamayı başlatır ve localhost üzerinde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="96d6b-258">İçin `http://localhost:<port_number>`bir tarayıcı açın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="96d6b-259">*Wwwroot/index.html* dosyası sunulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="96d6b-260">Adres çubuğundan URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="96d6b-261">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="96d6b-262">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="96d6b-263">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="96d6b-264">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![ileti hem tarayıcı penceresinde görüntüleniyor](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="96d6b-266">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="96d6b-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96d6b-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d6b-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="96d6b-268">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="96d6b-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="96d6b-269">.NET Core SDK 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="96d6b-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="96d6b-270">[NPM](https://www.npmjs.com/) ile [Node. js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="96d6b-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96d6b-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="96d6b-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="96d6b-273">.NET Core SDK 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="96d6b-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="96d6b-274">Visual Studio Code Version 1.17.1 veya üzeri için C#</span><span class="sxs-lookup"><span data-stu-id="96d6b-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="96d6b-275">[NPM](https://www.npmjs.com/) ile [Node. js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="96d6b-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="96d6b-276">ASP.NET Core Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="96d6b-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96d6b-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d6b-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96d6b-278">Visual Studio 'Yu, *Path* ortam değişkeninde NPM için arama yapmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="96d6b-279">Varsayılan olarak, Visual Studio yükleme dizininde bulunan NPM sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="96d6b-280">Visual Studio 'da şu yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="96d6b-281">**Araçlar** > **Options** Seçenekler > **Projeler ve çözümler** > **Web paket yönetimi** > **dış Web araçları**' na gidin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="96d6b-282">Listeden *$ (yol)* girişini seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="96d6b-283">Girdiyi listedeki ikinci konuma taşımak için yukarı oka tıklayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="96d6b-285">Visual Studio yapılandırması tamamlandı.</span><span class="sxs-lookup"><span data-stu-id="96d6b-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="96d6b-286">Projeyi oluşturma zamanı.</span><span class="sxs-lookup"><span data-stu-id="96d6b-286">It's time to create the project.</span></span>

1. <span data-ttu-id="96d6b-287">**Dosya** > **New** yeni > **Proje** menü seçeneğini kullanın ve **ASP.NET Core Web uygulaması** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="96d6b-288">Projeyi *Signalrwebpack*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="96d6b-289">Hedef çerçeve açılır listesinden *.NET Core* ' u seçin ve çerçeve Seçicisi açılır listesinden *ASP.NET Core 2,2* ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="96d6b-290">**Boş** şablonu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96d6b-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="96d6b-292">**Tümleşik terminalde**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="96d6b-293">.NET Core 'u hedefleyen boş bir ASP.NET Core Web uygulaması, bir *Signalrwebpack* dizininde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="96d6b-294">WebPack ve TypeScript yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="96d6b-295">Aşağıdaki adımlar, TypeScript 'in JavaScript 'e dönüştürülmesini ve istemci tarafı kaynaklarını paketlemeyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="96d6b-296">Bir *Package. JSON* dosyası oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="96d6b-297">Vurgulanan özelliği *Package. JSON* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="96d6b-298">`private` Özelliği, bir sonraki `true` adımda paket yükleme uyarılarını engelleyecek şekilde ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="96d6b-299">Gerekli NPM paketlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-299">Install the required npm packages.</span></span> <span data-ttu-id="96d6b-300">Proje kökünden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="96d6b-301">Aklınızda bazı komut ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="96d6b-301">Some command details to note:</span></span>

    * <span data-ttu-id="96d6b-302">Sürüm numarası her paket adı `@` için işareti izler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="96d6b-303">NPM bu özel paket sürümlerini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="96d6b-304">Seçeneği `-E` , NPM 'nin [semantik sürüm](https://semver.org/) aralığı işleçlerini *Package. JSON*öğesine yazmanın varsayılan davranışını devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="96d6b-305">Örneğin, `"webpack": "4.29.3"` yerine kullanılır `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="96d6b-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="96d6b-306">Bu seçenek, daha yeni paket sürümlerine istenmeden yükseltme yapılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="96d6b-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="96d6b-307">Daha fazla ayrıntı için bkz. [NPM-Install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="96d6b-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="96d6b-308">`scripts` *Package. JSON* dosyasının özelliğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="96d6b-309">Betiklerin bazı açıklamaları:</span><span class="sxs-lookup"><span data-stu-id="96d6b-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="96d6b-310">`build`: İstemci tarafı kaynaklarını geliştirme modunda paketler ve dosya değişikliklerini izler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="96d6b-311">Dosya izleyici, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="96d6b-312">`mode` Seçeneği, Tree gerçekleşmesi ve minbirleşme gibi üretim iyileştirmeleri devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="96d6b-313">Yalnızca geliştirme `build` aşamasında kullanın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="96d6b-314">`release`: İstemci tarafı kaynaklarını üretim modunda paketlayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="96d6b-315">`publish`: İstemci tarafı `release` kaynaklarını üretim modunda paketleyip betiği çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="96d6b-316">Uygulamayı yayımlamak için .NET Core CLI [Publish](/dotnet/core/tools/dotnet-publish) komutunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="96d6b-317">Aşağıdaki kodla, proje kökünde *WebPack. config. js* adlı bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="96d6b-318">Yukarıdaki dosya Web paketi derlemesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="96d6b-319">Aklınızda bazı yapılandırma ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="96d6b-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="96d6b-320">`output` Özelliği, *dağ*'ın varsayılan değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="96d6b-321">Paket, *Wwwroot* dizininde yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="96d6b-322">Dizi `resolve.extensions` , SignalR Istemci JavaScript 'i içeri aktarmak için *. js* içerir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="96d6b-323">Projenin istemci tarafı varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="96d6b-324">Aşağıdaki biçimlendirmeyle *src/index.html* oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="96d6b-325">Önceki HTML, giriş sayfasının ortak işaretlemesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="96d6b-326">Yeni bir *src/CSS* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="96d6b-327">Amacı, projenin *. css* dosyalarını depolamadır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="96d6b-328">Şu biçimlendirmeye sahip *src/CSS/Main. css* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="96d6b-329">Önceki *Main. css* dosyası uygulamayı stiller.</span><span class="sxs-lookup"><span data-stu-id="96d6b-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="96d6b-330">Şu JSON ile *src/tsconfig. JSON* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="96d6b-331">Yukarıdaki kod, TypeScript derleyicisini [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="96d6b-332">Aşağıdaki kodla *src/index. TS* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="96d6b-333">Önceki TypeScript, DOM öğelerine başvuruları alır ve iki olay işleyicisini ekler:</span><span class="sxs-lookup"><span data-stu-id="96d6b-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="96d6b-334">`keyup`: Bu olay Kullanıcı `tbMessage` metin kutusuna yazdığında ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="96d6b-335">Kullanıcı `send` **ENTER** tuşuna bastığında işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="96d6b-336">`click`: Kullanıcı **Gönder** düğmesine tıkladığında bu olay ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="96d6b-337">`send` işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="96d6b-338">ASP.NET Core uygulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="96d6b-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="96d6b-339">`Startup.Configure` Yönteminde belirtilen kod *Merhaba Dünya!* görüntülüyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="96d6b-340">`app.Run` Yöntem çağrısını [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [usestaticfiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağrılarıyla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="96d6b-341">Yukarıdaki kod, kullanıcının tam URL 'sini veya Web uygulamasının kök URL 'sini girmeksizin *Dizin. html* dosyasını bulmasını ve sunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="96d6b-342">`Startup.ConfigureServices`Içinde [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="96d6b-343">SignalR hizmetlerini projeye ekler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="96d6b-344">Bir */hub* yolunu `ChatHub` hub 'a eşleyin.</span><span class="sxs-lookup"><span data-stu-id="96d6b-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="96d6b-345">Aşağıdaki satırları sonuna ekleyin `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="96d6b-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="96d6b-346">Proje kökünde *hub*olarak adlandırılan yeni bir dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="96d6b-347">Amacı, bir sonraki adımda oluşturulan SignalR hub 'ını deposağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="96d6b-348">Aşağıdaki kodla hub *hub 'ları/ChatHub. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="96d6b-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="96d6b-349">Başvuruyu çözümlemek için Startup.cs dosyasının en üstüne aşağıdaki kodu ekleyin: *Startup.cs* `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="96d6b-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="96d6b-350">İstemci ve sunucu iletişimini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="96d6b-350">Enable client and server communication</span></span>

<span data-ttu-id="96d6b-351">Uygulama Şu anda ileti göndermek için basit bir form görüntülüyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="96d6b-352">Bunu yapmayı denediğinizde hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="96d6b-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="96d6b-353">Sunucu belirli bir yolu dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="96d6b-354">Proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="96d6b-355">Yukarıdaki komut, istemcisinin sunucuya ileti göndermesini sağlayan [SignalR TypeScript istemcisini](https://www.npmjs.com/package/@microsoft/signalr)yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="96d6b-356">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="96d6b-357">Yukarıdaki kod, sunucudan ileti almayı destekler.</span><span class="sxs-lookup"><span data-stu-id="96d6b-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="96d6b-358">`HubConnectionBuilder` Sınıfı, sunucu bağlantısını yapılandırmak için yeni bir Oluşturucu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="96d6b-359">`withUrl` İşlevi hub URL 'sini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="96d6b-360">SignalR, istemci ile sunucu arasında ileti alışverişi yapılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="96d6b-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="96d6b-361">Her ileti belirli bir ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-361">Each message has a specific name.</span></span> <span data-ttu-id="96d6b-362">Örneğin, adı `messageReceived` olan mesajlar ileti bölgesindeki yeni iletiyi görüntülemeden sorumlu mantığı çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="96d6b-363">Belirli bir iletiyi dinlemek, `on` işlevi aracılığıyla yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="96d6b-364">Herhangi bir sayıda ileti adını dinleyebilmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-364">You can listen to any number of message names.</span></span> <span data-ttu-id="96d6b-365">Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye geçirmek da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="96d6b-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="96d6b-366">İstemci bir ileti aldıktan sonra yazarın adı ve onun `div` `innerHTML` özniteliğinde ileti içeriğiyle yeni bir öğe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="96d6b-367">Yeni ileti, iletileri görüntüleyen Main `div` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="96d6b-368">Artık istemci bir ileti aldığına göre, ileti gönderecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="96d6b-369">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="96d6b-370">WebSockets bağlantısı aracılığıyla bir ileti gönderildiğinde `send` yönteminin çağrılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="96d6b-371">Yöntemin ilk parametresi ileti adıdır.</span><span class="sxs-lookup"><span data-stu-id="96d6b-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="96d6b-372">İleti verileri diğer parametreleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="96d6b-373">Bu örnekte, sunucusuna gönderildiği şekilde `newMessage` tanımlanan bir ileti.</span><span class="sxs-lookup"><span data-stu-id="96d6b-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="96d6b-374">İleti, bir metin kutusundan Kullanıcı adından ve Kullanıcı girişinden oluşur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="96d6b-375">Gönderme işlemi çalışırsa metin kutusu değeri temizlenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="96d6b-376">`NewMessage` yönetimini `ChatHub` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="96d6b-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="96d6b-377">Yukarıdaki kod, sunucu onları aldıktan sonra tüm bağlı kullanıcılara ileti almış iletileri yayınlar.</span><span class="sxs-lookup"><span data-stu-id="96d6b-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="96d6b-378">Tüm iletileri almak için genel `on` bir yöntem olması gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="96d6b-379">İleti adından sonra adlandırılmış bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="96d6b-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="96d6b-380">Bu örnekte, TypeScript istemcisi olarak `newMessage`tanımlanan bir ileti gönderir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="96d6b-381">C# `NewMessage` yöntemi, istemci tarafından gönderilen verileri bekliyor.</span><span class="sxs-lookup"><span data-stu-id="96d6b-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="96d6b-382">Istemcilerde [Sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) çağrısı yapılır [. tümü](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="96d6b-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="96d6b-383">Alınan iletiler, hub 'a bağlı tüm istemcilere gönderilir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="96d6b-384">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="96d6b-384">Test the app</span></span>

<span data-ttu-id="96d6b-385">Uygulamanın aşağıdaki adımlarla çalıştığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="96d6b-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96d6b-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d6b-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="96d6b-387">Web paketini *yayın* modunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="96d6b-388">**Paket Yöneticisi konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="96d6b-389">Proje kökünde değilseniz, komutu girmeden önce girin `cd SignalRWebPack` .</span><span class="sxs-lookup"><span data-stu-id="96d6b-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="96d6b-390">Hata ayıklayıcıyı eklemeden uygulamayı tarayıcıda başlatmak için hata ayıklama**olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="96d6b-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="96d6b-391">*Wwwroot/index.html* dosyası ' de `http://localhost:<port_number>`sunulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="96d6b-392">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="96d6b-393">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="96d6b-394">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="96d6b-395">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96d6b-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d6b-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="96d6b-397">Proje kökünde aşağıdaki komutu yürüterek Web paketini *yayın* modunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="96d6b-398">Proje kökünde aşağıdaki komutu yürüterek uygulamayı derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="96d6b-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="96d6b-399">Web sunucusu uygulamayı başlatır ve localhost üzerinde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="96d6b-400">İçin `http://localhost:<port_number>`bir tarayıcı açın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="96d6b-401">*Wwwroot/index.html* dosyası sunulur.</span><span class="sxs-lookup"><span data-stu-id="96d6b-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="96d6b-402">Adres çubuğundan URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="96d6b-403">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="96d6b-404">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="96d6b-405">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="96d6b-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="96d6b-406">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="96d6b-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![ileti hem tarayıcı penceresinde görüntüleniyor](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="96d6b-408">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="96d6b-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
