---
title: SignalRTypeScript ve WebPack ile ASP.NET Core kullanma
author: ssougnez
description: Bu öğreticide, Web paketini SignalR Istemcisi TypeScript 'te yazılmış bir ASP.NET Core Web uygulaması paketleyip derlemek üzere yapılandırırsınız.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 48b59fea5da3872fb29cacd9edbedd14de9e602f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019423"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="14551-103">SignalRTypeScript ve WebPack ile ASP.NET Core kullanma</span><span class="sxs-lookup"><span data-stu-id="14551-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="14551-104">, [Sébastien Sougnez](https://twitter.com/ssougnez) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="14551-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="14551-105">[WebPack](https://webpack.js.org/) , geliştiricilerin bir Web uygulamasının istemci tarafı kaynaklarını paketleyip oluşturmalarına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="14551-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="14551-106">Bu öğretici SignalR , Istemcisinin [TypeScript](https://www.typescriptlang.org/)'te yazıldığı bir ASP.NET Core Web uygulamasında WebPack 'in kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="14551-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="14551-107">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="14551-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14551-108">Bir başlatıcı ASP.NET Core SignalR uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="14551-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="14551-109">SignalRTypeScript istemcisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14551-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="14551-110">WebPack kullanarak derleme işlem hattı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14551-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="14551-111">Sunucuyu yapılandırma SignalR</span><span class="sxs-lookup"><span data-stu-id="14551-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="14551-112">İstemci ve sunucu arasındaki iletişimi etkinleştir</span><span class="sxs-lookup"><span data-stu-id="14551-112">Enable communication between client and server</span></span>

<span data-ttu-id="14551-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14551-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="14551-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="14551-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14551-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14551-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14551-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="14551-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="14551-117">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="14551-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="14551-118">[NPM](https://www.npmjs.com/) ile [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="14551-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14551-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="14551-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="14551-121">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="14551-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="14551-122">Visual Studio Code Version 1.17.1 veya üzeri için C#</span><span class="sxs-lookup"><span data-stu-id="14551-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="14551-123">[NPM](https://www.npmjs.com/) ile [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="14551-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="14551-124">ASP.NET Core Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="14551-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14551-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14551-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14551-126">Visual Studio 'Yu, *Path* ortam değişkeninde NPM için arama yapmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="14551-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="14551-127">Varsayılan olarak, Visual Studio yükleme dizininde bulunan NPM sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="14551-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="14551-128">Visual Studio 'da şu yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="14551-129">Visual Studio 'Yu başlatın.</span><span class="sxs-lookup"><span data-stu-id="14551-129">Launch Visual Studio.</span></span> <span data-ttu-id="14551-130">Başlangıç penceresinde, **kod olmadan devam et**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="14551-131">**Araçlar** > **Seçenekler** > **Projeler ve çözümler** > **Web paket yönetimi** > **dış Web araçları**' na gidin.</span><span class="sxs-lookup"><span data-stu-id="14551-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="14551-132">Listeden *$ (yol)* girişini seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="14551-133">Yukarı oka tıklayarak girişi listedeki ikinci konuma taşıyın ve **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Visual Studio yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="14551-135">Visual Studio yapılandırması tamamlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="14551-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="14551-136">**Dosya**  >  **Yeni**  >  **Proje** menü seçeneğini kullanın ve **ASP.NET Core Web uygulaması** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="14551-137">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-137">Select **Next**.</span></span>
1. <span data-ttu-id="14551-138">Projeyi \* SignalR WebPack\*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="14551-139">Hedef çerçeve açılır listesinden *.NET Core* ' u seçin ve çerçeve Seçicisi açılır listesinden *ASP.NET Core 3,1* ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="14551-140">**Boş** şablonu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="14551-141">`Microsoft.TypeScript.MSBuild`Paketi projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="14551-142">**Çözüm Gezgini** (sağ bölme) içinde, proje düğümüne sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="14551-143">Araştır sekmesine **gidin** `Microsoft.TypeScript.MSBuild` ve ardından paketi yüklemek için sağ tarafta bulunan **Install** ara ' yı tıklatın.</span><span class="sxs-lookup"><span data-stu-id="14551-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="14551-144">Visual Studio, NuGet paketini **Çözüm Gezgini**' deki **Bağımlılıklar** düğümüne ekler ve projede TypeScript derlemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="14551-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14551-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="14551-146">**Tümleşik terminalde**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="14551-147">`dotnet new`Komut, bir \* SignalR WebPack\* dizininde boş bir ASP.NET Core Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14551-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="14551-148">`code`Komut, Visual Studio Code geçerli örneğindeki \* SignalR WebPack\* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="14551-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="14551-149">**Tümleşik terminalde**aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="14551-150">Yukarıdaki komut, [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) paketini ekler ve projede TypeScript derlemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="14551-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="14551-151">WebPack ve TypeScript yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14551-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="14551-152">Aşağıdaki adımlar, TypeScript 'in JavaScript 'e dönüştürülmesini ve istemci tarafı kaynaklarını paketlemeyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="14551-153">Dosyasında bir *package.js* oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="14551-154">Vurgulanan özelliği dosyadaki *package.js* ekleyin ve dosya değişikliklerini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="14551-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="14551-155">Özelliği, bir `private` `true` sonraki adımda paket yükleme uyarılarını engelleyecek şekilde ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="14551-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="14551-156">Gerekli NPM paketlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="14551-156">Install the required npm packages.</span></span> <span data-ttu-id="14551-157">Proje kökünden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="14551-158">Aklınızda bazı komut ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="14551-158">Some command details to note:</span></span>

    * <span data-ttu-id="14551-159">Sürüm numarası `@` her paket adı için işareti izler.</span><span class="sxs-lookup"><span data-stu-id="14551-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="14551-160">NPM bu özel paket sürümlerini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="14551-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="14551-161">Bu `-E` seçenek, NPM 'nin [anlam sürümü oluşturma](https://semver.org/) aralığı işleçlerini *package.js*için varsayılan davranışını devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="14551-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="14551-162">Örneğin, `"webpack": "4.41.5"` yerine kullanılır `"webpack": "^4.41.5"` .</span><span class="sxs-lookup"><span data-stu-id="14551-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="14551-163">Bu seçenek, daha yeni paket sürümlerine istenmeden yükseltme yapılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="14551-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="14551-164">Daha fazla ayrıntı için bkz. [NPM-Install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="14551-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="14551-165">`scripts`Dosyadaki *package.js* özelliğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="14551-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="14551-166">Betiklerin bazı açıklamaları:</span><span class="sxs-lookup"><span data-stu-id="14551-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="14551-167">`build`: İstemci tarafı kaynaklarını geliştirme modunda paketler ve dosya değişikliklerini izler.</span><span class="sxs-lookup"><span data-stu-id="14551-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="14551-168">Dosya izleyici, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="14551-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="14551-169">`mode`Seçeneği, Tree gerçekleşmesi ve minbirleşme gibi üretim iyileştirmeleri devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="14551-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="14551-170">Yalnızca `build` geliştirme aşamasında kullanın.</span><span class="sxs-lookup"><span data-stu-id="14551-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="14551-171">`release`: İstemci tarafı kaynaklarını üretim modunda paketlayın.</span><span class="sxs-lookup"><span data-stu-id="14551-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="14551-172">`publish`: `release` İstemci tarafı kaynaklarını üretim modunda paketleyip betiği çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="14551-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="14551-173">Uygulamayı yayımlamak için .NET Core CLI [Publish](/dotnet/core/tools/dotnet-publish) komutunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="14551-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="14551-174">Proje kökünde aşağıdaki kodla *webpack.config.js*adlı bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="14551-175">Yukarıdaki dosya Web paketi derlemesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="14551-176">Aklınızda bazı yapılandırma ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="14551-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="14551-177">`output`Özelliği, *dağ*'ın varsayılan değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="14551-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="14551-178">Paket, *Wwwroot* dizininde yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="14551-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="14551-179">`resolve.extensions`Dizi, Istemci JavaScript 'i içeri aktarmak için *. js* içerir SignalR .</span><span class="sxs-lookup"><span data-stu-id="14551-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="14551-180">Projenin istemci tarafı varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="14551-181">Aşağıdaki işaretle *src/index.html* oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="14551-182">Önceki HTML, giriş sayfasının ortak işaretlemesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="14551-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="14551-183">Yeni bir *src/CSS* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="14551-184">Amacı, projenin *. css* dosyalarını depolamadır.</span><span class="sxs-lookup"><span data-stu-id="14551-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="14551-185">Şu CSS ile *src/CSS/Main. css* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="14551-186">Önceki *Main. css* dosyası uygulamayı stiller.</span><span class="sxs-lookup"><span data-stu-id="14551-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="14551-187">Aşağıdaki JSON ile *src/tsconfig.js* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="14551-188">Yukarıdaki kod, TypeScript derleyicisini [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="14551-189">Aşağıdaki kodla *src/index. TS* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="14551-190">Önceki TypeScript, DOM öğelerine başvuruları alır ve iki olay işleyicisini ekler:</span><span class="sxs-lookup"><span data-stu-id="14551-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="14551-191">`keyup`: Bu olay Kullanıcı `tbMessage` metin kutusuna yazdığında ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="14551-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="14551-192">`send`Kullanıcı **ENTER** tuşuna bastığında işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="14551-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="14551-193">`click`: Kullanıcı **Gönder** düğmesine tıkladığında bu olay ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="14551-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="14551-194">`send` işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="14551-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="14551-195">Uygulamayı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14551-195">Configure the app</span></span>

1. <span data-ttu-id="14551-196">İçinde `Startup.Configure` , [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [usestaticfiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)öğesine çağrılar ekleyin.</span><span class="sxs-lookup"><span data-stu-id="14551-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="14551-197">Yukarıdaki kod, sunucunun *index.html* dosyasını bulmasını ve sunması için izin verir.</span><span class="sxs-lookup"><span data-stu-id="14551-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="14551-198">Dosya, kullanıcının tam URL 'sini veya Web uygulamasının kök URL 'sini girip girmediğini görür.</span><span class="sxs-lookup"><span data-stu-id="14551-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="14551-199">Sonunda `Startup.Configure` , bir */hub* yolunu hub 'a eşleyin `ChatHub` .</span><span class="sxs-lookup"><span data-stu-id="14551-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="14551-200">Merhaba Dünya görüntülenen kodu değiştirin *!*</span><span class="sxs-lookup"><span data-stu-id="14551-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="14551-201">Aşağıdaki satırla:</span><span class="sxs-lookup"><span data-stu-id="14551-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="14551-202">İçinde `Startup.ConfigureServices` , [Ekle SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="14551-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="14551-203">Hub 'ı depolamak için proje kök \* SignalR WebPack/\* içindeki *hub* adlı yeni bir dizin oluşturun SignalR .</span><span class="sxs-lookup"><span data-stu-id="14551-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="14551-204">Aşağıdaki kodla hub *hub 'ları/ChatHub. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="14551-205">`using`Başvuruyu çözümlemek için *Startup.cs* dosyasının en üstüne aşağıdaki ifadeyi ekleyin `ChatHub` :</span><span class="sxs-lookup"><span data-stu-id="14551-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="14551-206">İstemci ve sunucu iletişimini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="14551-206">Enable client and server communication</span></span>

<span data-ttu-id="14551-207">Uygulama Şu anda ileti göndermek için temel bir form görüntülüyor, ancak henüz işlevsel değil.</span><span class="sxs-lookup"><span data-stu-id="14551-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="14551-208">Sunucu belirli bir yolu dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.</span><span class="sxs-lookup"><span data-stu-id="14551-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="14551-209">Proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="14551-210">Önceki komut yüklenir:</span><span class="sxs-lookup"><span data-stu-id="14551-210">The preceding command installs:</span></span>

     * <span data-ttu-id="14551-211">İstemcinin sunucuya ileti göndermesini sağlayan [ SignalR TypeScript istemcisi](https://www.npmjs.com/package/@microsoft/signalr).</span><span class="sxs-lookup"><span data-stu-id="14551-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="14551-212">Node.js türlerin derleme zamanı denetimini sağlayan Node.js için TypeScript tür tanımları.</span><span class="sxs-lookup"><span data-stu-id="14551-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="14551-213">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="14551-214">Yukarıdaki kod, sunucudan ileti almayı destekler.</span><span class="sxs-lookup"><span data-stu-id="14551-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="14551-215">`HubConnectionBuilder`Sınıfı, sunucu bağlantısını yapılandırmak için yeni bir Oluşturucu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14551-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="14551-216">`withUrl`İşlevi hub URL 'sini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="14551-217">SignalRistemci ve sunucu arasında ileti alışverişi yapılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="14551-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="14551-218">Her ileti belirli bir ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="14551-218">Each message has a specific name.</span></span> <span data-ttu-id="14551-219">Örneğin, adı olan mesajlar ileti `messageReceived` bölgesindeki yeni iletiyi görüntülemeden sorumlu mantığı çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="14551-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="14551-220">Belirli bir iletiyi dinlemek, işlevi aracılığıyla yapılabilir `on` .</span><span class="sxs-lookup"><span data-stu-id="14551-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="14551-221">Herhangi bir sayıda ileti adı ile listenlebilir.</span><span class="sxs-lookup"><span data-stu-id="14551-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="14551-222">Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye geçirmek da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="14551-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="14551-223">İstemci bir ileti aldıktan sonra `div` yazarın adı ve onun özniteliğinde ileti içeriğiyle yeni bir öğe oluşturulur `innerHTML` .</span><span class="sxs-lookup"><span data-stu-id="14551-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="14551-224">`div`İletileri görüntüleyen Main öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="14551-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="14551-225">Artık istemci bir ileti aldığına göre, ileti gönderecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="14551-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="14551-226">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="14551-227">WebSockets bağlantısı aracılığıyla bir ileti gönderildiğinde yönteminin çağrılması gerekir `send` .</span><span class="sxs-lookup"><span data-stu-id="14551-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="14551-228">Yöntemin ilk parametresi ileti adıdır.</span><span class="sxs-lookup"><span data-stu-id="14551-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="14551-229">İleti verileri diğer parametreleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="14551-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="14551-230">Bu örnekte, sunucusuna gönderildiği şekilde tanımlanan bir ileti `newMessage` .</span><span class="sxs-lookup"><span data-stu-id="14551-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="14551-231">İleti, bir metin kutusundan Kullanıcı adından ve Kullanıcı girişinden oluşur.</span><span class="sxs-lookup"><span data-stu-id="14551-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="14551-232">Gönderme işlemi çalışırsa metin kutusu değeri temizlenir.</span><span class="sxs-lookup"><span data-stu-id="14551-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="14551-233">`NewMessage` yönetimini `ChatHub` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="14551-234">Yukarıdaki kod, sunucu onları aldıktan sonra tüm bağlı kullanıcılara ileti almış iletileri yayınlar.</span><span class="sxs-lookup"><span data-stu-id="14551-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="14551-235">`on`Tüm iletileri almak için genel bir yöntem olması gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="14551-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="14551-236">İleti adından sonra adlandırılmış bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="14551-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="14551-237">Bu örnekte, TypeScript istemcisi olarak tanımlanan bir ileti gönderir `newMessage` .</span><span class="sxs-lookup"><span data-stu-id="14551-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="14551-238">C# `NewMessage` yöntemi, istemci tarafından gönderilen verileri bekliyor.</span><span class="sxs-lookup"><span data-stu-id="14551-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="14551-239">Istemcilerde [Sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) çağrısı yapılır [. tümü](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="14551-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="14551-240">Alınan iletiler, hub 'a bağlı tüm istemcilere gönderilir.</span><span class="sxs-lookup"><span data-stu-id="14551-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="14551-241">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="14551-241">Test the app</span></span>

<span data-ttu-id="14551-242">Uygulamanın aşağıdaki adımlarla çalıştığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="14551-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14551-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14551-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="14551-244">Web paketini *yayın* modunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="14551-245">**Paket Yöneticisi konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="14551-246">Proje kökünde değilseniz, `cd SignalRWebPack` komutu girmeden önce girin.</span><span class="sxs-lookup"><span data-stu-id="14551-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="14551-247">Hata **Debug**  >  ayıklayıcıyı eklemeden uygulamayı tarayıcıda başlatmak için hata ayıklama**olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="14551-248">*Wwwroot/index.html* dosyası ' de sunulur `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="14551-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="14551-249">Derleme hataları alırsanız, çözümü kapatıp yeniden açmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="14551-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="14551-250">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="14551-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="14551-251">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14551-252">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="14551-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="14551-253">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="14551-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14551-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="14551-255">Proje kökünde aşağıdaki komutu yürüterek Web paketini *yayın* modunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="14551-256">Proje kökünde aşağıdaki komutu yürüterek uygulamayı derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="14551-257">Web sunucusu uygulamayı başlatır ve localhost üzerinde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="14551-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="14551-258">İçin bir tarayıcı açın `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="14551-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="14551-259">*Wwwroot/index.html* dosyası sunulur.</span><span class="sxs-lookup"><span data-stu-id="14551-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="14551-260">Adres çubuğundan URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="14551-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="14551-261">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="14551-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="14551-262">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14551-263">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="14551-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="14551-264">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="14551-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![ileti hem tarayıcı penceresinde görüntüleniyor](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="14551-266">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="14551-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14551-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14551-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14551-268">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="14551-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="14551-269">.NET Core SDK 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="14551-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="14551-270">[NPM](https://www.npmjs.com/) ile [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="14551-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14551-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="14551-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="14551-273">.NET Core SDK 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="14551-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="14551-274">Visual Studio Code Version 1.17.1 veya üzeri için C#</span><span class="sxs-lookup"><span data-stu-id="14551-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="14551-275">[NPM](https://www.npmjs.com/) ile [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="14551-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="14551-276">ASP.NET Core Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="14551-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14551-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14551-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14551-278">Visual Studio 'Yu, *Path* ortam değişkeninde NPM için arama yapmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="14551-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="14551-279">Varsayılan olarak, Visual Studio yükleme dizininde bulunan NPM sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="14551-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="14551-280">Visual Studio 'da şu yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="14551-281">**Araçlar** > **Seçenekler** > **Projeler ve çözümler** > **Web paket yönetimi** > **dış Web araçları**' na gidin.</span><span class="sxs-lookup"><span data-stu-id="14551-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="14551-282">Listeden *$ (yol)* girişini seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="14551-283">Girdiyi listedeki ikinci konuma taşımak için yukarı oka tıklayın.</span><span class="sxs-lookup"><span data-stu-id="14551-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="14551-285">Visual Studio yapılandırması tamamlandı.</span><span class="sxs-lookup"><span data-stu-id="14551-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="14551-286">Projeyi oluşturma zamanı.</span><span class="sxs-lookup"><span data-stu-id="14551-286">It's time to create the project.</span></span>

1. <span data-ttu-id="14551-287">**Dosya** > **Yeni** > **Proje** menü seçeneğini kullanın ve **ASP.NET Core Web uygulaması** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="14551-288">Projeyi \* SignalR WebPack\*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="14551-289">Hedef çerçeve açılır listesinden *.NET Core* ' u seçin ve çerçeve Seçicisi açılır listesinden *ASP.NET Core 2,2* ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="14551-290">**Boş** şablonu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14551-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="14551-292">**Tümleşik terminalde**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="14551-293">.NET Core 'u hedefleyen boş bir ASP.NET Core Web uygulaması bir \* SignalR WebPack\* dizininde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="14551-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="14551-294">WebPack ve TypeScript yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14551-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="14551-295">Aşağıdaki adımlar, TypeScript 'in JavaScript 'e dönüştürülmesini ve istemci tarafı kaynaklarını paketlemeyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="14551-296">Dosyasında bir *package.js* oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="14551-297">Vurgulanan özelliği dosyadaki *package.js* ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="14551-298">Özelliği, bir `private` `true` sonraki adımda paket yükleme uyarılarını engelleyecek şekilde ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="14551-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="14551-299">Gerekli NPM paketlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="14551-299">Install the required npm packages.</span></span> <span data-ttu-id="14551-300">Proje kökünden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="14551-301">Aklınızda bazı komut ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="14551-301">Some command details to note:</span></span>

    * <span data-ttu-id="14551-302">Sürüm numarası `@` her paket adı için işareti izler.</span><span class="sxs-lookup"><span data-stu-id="14551-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="14551-303">NPM bu özel paket sürümlerini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="14551-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="14551-304">Bu `-E` seçenek, NPM 'nin [anlam sürümü oluşturma](https://semver.org/) aralığı işleçlerini *package.js*için varsayılan davranışını devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="14551-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="14551-305">Örneğin, `"webpack": "4.29.3"` yerine kullanılır `"webpack": "^4.29.3"` .</span><span class="sxs-lookup"><span data-stu-id="14551-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="14551-306">Bu seçenek, daha yeni paket sürümlerine istenmeden yükseltme yapılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="14551-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="14551-307">Daha fazla ayrıntı için bkz. [NPM-Install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="14551-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="14551-308">`scripts`Dosyadaki *package.js* özelliğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="14551-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="14551-309">Betiklerin bazı açıklamaları:</span><span class="sxs-lookup"><span data-stu-id="14551-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="14551-310">`build`: İstemci tarafı kaynaklarını geliştirme modunda paketler ve dosya değişikliklerini izler.</span><span class="sxs-lookup"><span data-stu-id="14551-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="14551-311">Dosya izleyici, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="14551-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="14551-312">`mode`Seçeneği, Tree gerçekleşmesi ve minbirleşme gibi üretim iyileştirmeleri devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="14551-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="14551-313">Yalnızca `build` geliştirme aşamasında kullanın.</span><span class="sxs-lookup"><span data-stu-id="14551-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="14551-314">`release`: İstemci tarafı kaynaklarını üretim modunda paketlayın.</span><span class="sxs-lookup"><span data-stu-id="14551-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="14551-315">`publish`: `release` İstemci tarafı kaynaklarını üretim modunda paketleyip betiği çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="14551-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="14551-316">Uygulamayı yayımlamak için .NET Core CLI [Publish](/dotnet/core/tools/dotnet-publish) komutunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="14551-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="14551-317">Proje kökünde aşağıdaki kodla *webpack.config.js* adlı bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="14551-318">Yukarıdaki dosya Web paketi derlemesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="14551-319">Aklınızda bazı yapılandırma ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="14551-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="14551-320">`output`Özelliği, *dağ*'ın varsayılan değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="14551-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="14551-321">Paket, *Wwwroot* dizininde yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="14551-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="14551-322">`resolve.extensions`Dizi, Istemci JavaScript 'i içeri aktarmak için *. js* içerir SignalR .</span><span class="sxs-lookup"><span data-stu-id="14551-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="14551-323">Projenin istemci tarafı varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="14551-324">Aşağıdaki işaretle *src/index.html* oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="14551-325">Önceki HTML, giriş sayfasının ortak işaretlemesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="14551-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="14551-326">Yeni bir *src/CSS* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="14551-327">Amacı, projenin *. css* dosyalarını depolamadır.</span><span class="sxs-lookup"><span data-stu-id="14551-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="14551-328">Şu biçimlendirmeye sahip *src/CSS/Main. css* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="14551-329">Önceki *Main. css* dosyası uygulamayı stiller.</span><span class="sxs-lookup"><span data-stu-id="14551-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="14551-330">Aşağıdaki JSON ile *src/tsconfig.js* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="14551-331">Yukarıdaki kod, TypeScript derleyicisini [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="14551-332">Aşağıdaki kodla *src/index. TS* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="14551-333">Önceki TypeScript, DOM öğelerine başvuruları alır ve iki olay işleyicisini ekler:</span><span class="sxs-lookup"><span data-stu-id="14551-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="14551-334">`keyup`: Bu olay Kullanıcı `tbMessage` metin kutusuna yazdığında ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="14551-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="14551-335">`send`Kullanıcı **ENTER** tuşuna bastığında işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="14551-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="14551-336">`click`: Kullanıcı **Gönder** düğmesine tıkladığında bu olay ateşlenir.</span><span class="sxs-lookup"><span data-stu-id="14551-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="14551-337">`send` işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="14551-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="14551-338">ASP.NET Core uygulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14551-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="14551-339">Yönteminde belirtilen kod `Startup.Configure` *Merhaba Dünya!* görüntülüyor.</span><span class="sxs-lookup"><span data-stu-id="14551-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="14551-340">`app.Run`Yöntem çağrısını [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [usestaticfiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağrılarıyla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="14551-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="14551-341">Yukarıdaki kod, kullanıcının tam URL 'sini veya Web uygulamasının kök URL 'sini girip girmediğini *index.html* dosyasını bulmasını ve sunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="14551-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="14551-342">[Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) eklentisini çağırın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="14551-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14551-343">SignalRHizmetleri projeye ekler.</span><span class="sxs-lookup"><span data-stu-id="14551-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="14551-344">Bir */hub* yolunu hub 'a eşleyin `ChatHub` .</span><span class="sxs-lookup"><span data-stu-id="14551-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="14551-345">Aşağıdaki satırları sonuna ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="14551-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="14551-346">Proje kökünde *hub*olarak adlandırılan yeni bir dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="14551-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="14551-347">Amacı, bir SignalR sonraki adımda oluşturulan hub 'ı deposağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="14551-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="14551-348">Aşağıdaki kodla hub *hub 'ları/ChatHub. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="14551-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="14551-349">Başvuruyu çözümlemek için *Startup.cs* dosyasının en üstüne aşağıdaki kodu ekleyin `ChatHub` :</span><span class="sxs-lookup"><span data-stu-id="14551-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="14551-350">İstemci ve sunucu iletişimini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="14551-350">Enable client and server communication</span></span>

<span data-ttu-id="14551-351">Uygulama Şu anda ileti göndermek için basit bir form görüntülüyor.</span><span class="sxs-lookup"><span data-stu-id="14551-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="14551-352">Bunu yapmayı denediğinizde hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="14551-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="14551-353">Sunucu belirli bir yolu dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.</span><span class="sxs-lookup"><span data-stu-id="14551-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="14551-354">Proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="14551-355">Yukarıdaki komut, istemcinin sunucuya ileti göndermesini sağlayan [ SignalR TypeScript istemcisini](https://www.npmjs.com/package/@microsoft/signalr)yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="14551-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="14551-356">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="14551-357">Yukarıdaki kod, sunucudan ileti almayı destekler.</span><span class="sxs-lookup"><span data-stu-id="14551-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="14551-358">`HubConnectionBuilder`Sınıfı, sunucu bağlantısını yapılandırmak için yeni bir Oluşturucu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14551-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="14551-359">`withUrl`İşlevi hub URL 'sini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="14551-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="14551-360">SignalRistemci ve sunucu arasında ileti alışverişi yapılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="14551-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="14551-361">Her ileti belirli bir ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="14551-361">Each message has a specific name.</span></span> <span data-ttu-id="14551-362">Örneğin, adı olan mesajlar ileti `messageReceived` bölgesindeki yeni iletiyi görüntülemeden sorumlu mantığı çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="14551-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="14551-363">Belirli bir iletiyi dinlemek, işlevi aracılığıyla yapılabilir `on` .</span><span class="sxs-lookup"><span data-stu-id="14551-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="14551-364">Herhangi bir sayıda ileti adını dinleyebilmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="14551-364">You can listen to any number of message names.</span></span> <span data-ttu-id="14551-365">Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye geçirmek da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="14551-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="14551-366">İstemci bir ileti aldıktan sonra `div` yazarın adı ve onun özniteliğinde ileti içeriğiyle yeni bir öğe oluşturulur `innerHTML` .</span><span class="sxs-lookup"><span data-stu-id="14551-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="14551-367">Yeni ileti, `div` iletileri görüntüleyen Main öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="14551-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="14551-368">Artık istemci bir ileti aldığına göre, ileti gönderecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="14551-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="14551-369">Vurgulanan kodu *src/index. TS* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="14551-370">WebSockets bağlantısı aracılığıyla bir ileti gönderildiğinde yönteminin çağrılması gerekir `send` .</span><span class="sxs-lookup"><span data-stu-id="14551-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="14551-371">Yöntemin ilk parametresi ileti adıdır.</span><span class="sxs-lookup"><span data-stu-id="14551-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="14551-372">İleti verileri diğer parametreleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="14551-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="14551-373">Bu örnekte, sunucusuna gönderildiği şekilde tanımlanan bir ileti `newMessage` .</span><span class="sxs-lookup"><span data-stu-id="14551-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="14551-374">İleti, bir metin kutusundan Kullanıcı adından ve Kullanıcı girişinden oluşur.</span><span class="sxs-lookup"><span data-stu-id="14551-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="14551-375">Gönderme işlemi çalışırsa metin kutusu değeri temizlenir.</span><span class="sxs-lookup"><span data-stu-id="14551-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="14551-376">`NewMessage` yönetimini `ChatHub` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="14551-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="14551-377">Yukarıdaki kod, sunucu onları aldıktan sonra tüm bağlı kullanıcılara ileti almış iletileri yayınlar.</span><span class="sxs-lookup"><span data-stu-id="14551-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="14551-378">`on`Tüm iletileri almak için genel bir yöntem olması gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="14551-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="14551-379">İleti adından sonra adlandırılmış bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="14551-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="14551-380">Bu örnekte, TypeScript istemcisi olarak tanımlanan bir ileti gönderir `newMessage` .</span><span class="sxs-lookup"><span data-stu-id="14551-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="14551-381">C# `NewMessage` yöntemi, istemci tarafından gönderilen verileri bekliyor.</span><span class="sxs-lookup"><span data-stu-id="14551-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="14551-382">Istemcilerde [Sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) çağrısı yapılır [. tümü](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="14551-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="14551-383">Alınan iletiler, hub 'a bağlı tüm istemcilere gönderilir.</span><span class="sxs-lookup"><span data-stu-id="14551-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="14551-384">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="14551-384">Test the app</span></span>

<span data-ttu-id="14551-385">Uygulamanın aşağıdaki adımlarla çalıştığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="14551-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14551-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14551-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="14551-387">Web paketini *yayın* modunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="14551-388">**Paket Yöneticisi konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="14551-389">Proje kökünde değilseniz, `cd SignalRWebPack` komutu girmeden önce girin.</span><span class="sxs-lookup"><span data-stu-id="14551-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="14551-390">Hata **Debug**  >  ayıklayıcıyı eklemeden uygulamayı tarayıcıda başlatmak için hata ayıklama**olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="14551-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="14551-391">*Wwwroot/index.html* dosyası ' de sunulur `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="14551-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="14551-392">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="14551-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="14551-393">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14551-394">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="14551-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="14551-395">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="14551-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14551-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14551-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="14551-397">Proje kökünde aşağıdaki komutu yürüterek Web paketini *yayın* modunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="14551-398">Proje kökünde aşağıdaki komutu yürüterek uygulamayı derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="14551-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="14551-399">Web sunucusu uygulamayı başlatır ve localhost üzerinde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="14551-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="14551-400">İçin bir tarayıcı açın `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="14551-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="14551-401">*Wwwroot/index.html* dosyası sunulur.</span><span class="sxs-lookup"><span data-stu-id="14551-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="14551-402">Adres çubuğundan URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="14551-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="14551-403">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="14551-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="14551-404">URL 'YI adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="14551-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14551-405">Tarayıcı seçin, **ileti** metin kutusuna bir şey yazın ve **Gönder** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="14551-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="14551-406">Benzersiz Kullanıcı adı ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="14551-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![ileti hem tarayıcı penceresinde görüntüleniyor](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="14551-408">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14551-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
