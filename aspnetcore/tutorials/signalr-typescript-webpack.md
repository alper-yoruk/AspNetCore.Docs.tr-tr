---
title: TypeScript SignalR ve Webpack ile ASP.NET Core'u kullanma
author: ssougnez
description: Bu eğitimde, Webpack'i, istemcisi SignalR TypeScript'te yazılmış bir ASP.NET Core web uygulamasını paketleyip oluşturmak üzere yapılandırmış sınız.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511346"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="f35ef-103">TypeScript ve Webpack ile ASP.NET Core SignalR kullanın</span><span class="sxs-lookup"><span data-stu-id="f35ef-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="f35ef-104">Yazar: [Sébastien Sougnez](https://twitter.com/ssougnez) ve [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="f35ef-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="f35ef-105">[Webpack,](https://webpack.js.org/) geliştiricilerin bir web uygulamasının istemci tarafındaki kaynaklarını paketleyip oluşturmalarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="f35ef-106">Bu öğretici, [istemcisi TypeScript'te](https://www.typescriptlang.org/)yazılmış bir ASP.NET Core SignalR web uygulamasında Webpack'in kullanılmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="f35ef-107">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f35ef-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f35ef-108">İskele Core SignalR uygulaması ASP.NET bir marş</span><span class="sxs-lookup"><span data-stu-id="f35ef-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="f35ef-109">SignalR TypeScript istemcisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f35ef-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="f35ef-110">Webpack'i kullanarak yapı ardışık yapılı</span><span class="sxs-lookup"><span data-stu-id="f35ef-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="f35ef-111">SignalR sunucusunu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f35ef-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="f35ef-112">İstemci ve sunucu arasındaki iletişimi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f35ef-112">Enable communication between client and server</span></span>

<span data-ttu-id="f35ef-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f35ef-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="f35ef-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f35ef-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f35ef-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f35ef-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f35ef-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile</span><span class="sxs-lookup"><span data-stu-id="f35ef-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="f35ef-117">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="f35ef-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="f35ef-118">[Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="f35ef-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f35ef-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="f35ef-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="f35ef-121">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="f35ef-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="f35ef-122">Visual Studio Code sürüm 1.17.1 veya sonrası için C#</span><span class="sxs-lookup"><span data-stu-id="f35ef-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="f35ef-123">[Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="f35ef-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="f35ef-124">ASP.NET Core web uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f35ef-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f35ef-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f35ef-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f35ef-126">Visual Studio'yu *PATH* ortamı değişkeninde npm'yi arayacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="f35ef-127">Varsayılan olarak, Visual Studio yükleme dizininde bulunan npm sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="f35ef-128">Visual Studio'da şu talimatları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="f35ef-129">Visual Studio’yu başlatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-129">Launch Visual Studio.</span></span> <span data-ttu-id="f35ef-130">Başlangıç penceresinde, **kodsuz Devam**et'i seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="f35ef-131">**Araçlar** > **Seçenekleri** > **Projeler ve Çözümler** > **Web Paket Yönetimi** > **Dış Web Araçları**gidin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="f35ef-132">Listeden *$(PATH)* girişini seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="f35ef-133">Girişi listedeki ikinci konuma taşımak için yukarı ok'u tıklatın ve **Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Görsel Stüdyo Yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="f35ef-135">Visual Studio yapılandırması tamamlandı.</span><span class="sxs-lookup"><span data-stu-id="f35ef-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="f35ef-136"> > **New**Yeni >  **Proje Dosyası**menüsü seçeneğini kullanın ve **ASP.NET Çekirdek Web Uygulaması** şablonuna gidin.**Project**</span><span class="sxs-lookup"><span data-stu-id="f35ef-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="f35ef-137">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-137">Select **Next**.</span></span>
1. <span data-ttu-id="f35ef-138">Projeye *SignalRWebPack*adını ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="f35ef-139">Hedef çerçeve açılır tarafından *.NET Core'u* seçin ve çerçeve seçici açılır ASP.NET *Core 3.1'i* seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="f35ef-140">Boş **şablonu** seçin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="f35ef-141">`Microsoft.TypeScript.MSBuild` Paketi projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="f35ef-142">**Çözüm Gezgini'nde** (sağ bölme), proje düğümüne sağ tıklayın ve **NuGet Paketlerini Yönet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="f35ef-143">**Gözat** sekmesinde, paketi `Microsoft.TypeScript.MSBuild`yüklemek için sağ tarafta Yükle'yi arayın ve ardından **Yükle'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="f35ef-144">Visual Studio, **NuGet**paketini Solution Explorer'daki **Bağımlılıkdüğümün** altına ekler ve projede TypeScript derlemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f35ef-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f35ef-146">**Entegre Terminal'de**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="f35ef-147">Komut, `dotnet new` *SignalRWebPack* dizininde boş bir ASP.NET Core web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="f35ef-148">Komut, `code` Visual Studio Code'un geçerli örneğinde *SignalRWebPack* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="f35ef-149">**Entegre Terminalde**aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="f35ef-150">Önceki komut, projede TypeScript derlemesini etkinleştiren [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) paketini ekler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="f35ef-151">Webpack ve TypeScript yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f35ef-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="f35ef-152">Aşağıdaki adımlar, TypeScript'in JavaScript'e dönüştürülmesini ve istemci tarafındaki kaynakların birbirlemasını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="f35ef-153">*Package.json* dosyası oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="f35ef-154">Vurgulanan özelliği *package.json* dosyasına ekleyin ve dosya değişikliklerini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="f35ef-155">`private` Bir sonraki `true` adımda paket yükleme uyarılarını engelleyecek şekilde özelliği ayarlama.</span><span class="sxs-lookup"><span data-stu-id="f35ef-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="f35ef-156">Gerekli npm paketlerini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-156">Install the required npm packages.</span></span> <span data-ttu-id="f35ef-157">Proje kökünden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="f35ef-158">Dikkat edilmesi gereken bazı komut ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="f35ef-158">Some command details to note:</span></span>

    * <span data-ttu-id="f35ef-159">Her paket adı `@` için bir sürüm numarası işareti izler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="f35ef-160">npm bu özel paket sürümlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="f35ef-161">Seçenek, `-E` npm'nin [semantik sürüm](https://semver.org/) aralığı işleçlerini *package.json'a*yazma varsayılan davranışını devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="f35ef-162">Örneğin, `"webpack": "4.41.5"` `"webpack": "^4.41.5"`".</span><span class="sxs-lookup"><span data-stu-id="f35ef-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="f35ef-163">Bu seçenek, yeni paket sürümlerinde istenmeyen yükseltmeleri önler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="f35ef-164">Daha fazla ayrıntı için [npm-install](https://docs.npmjs.com/cli/install) dokümanlarına bakın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="f35ef-165">`scripts` *Package.json* dosyasının özelliğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="f35ef-166">Komut dosyalarının bazı açıklamaları:</span><span class="sxs-lookup"><span data-stu-id="f35ef-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="f35ef-167">`build`: İstemci tarafındaki kaynakları geliştirme modunda paketler ve dosya değişikliklerini izler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="f35ef-168">Dosya izleyicisi, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="f35ef-169">Bu `mode` seçenek, ağaç sallayarak ve minification gibi üretim optimizasyonları devre dışı bırakmaz.</span><span class="sxs-lookup"><span data-stu-id="f35ef-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="f35ef-170">Yalnızca `build` geliştirmede kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="f35ef-171">`release`: İstemci tarafındaki kaynakları üretim modunda paketler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="f35ef-172">`publish`: İstemci tarafındaki kaynakları üretim modunda bir araya getirmek için komut dosyasını `release` çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="f35ef-173">.NET Core CLI'nin uygulamayı yayımlamak için [yayımlama](/dotnet/core/tools/dotnet-publish) komutunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="f35ef-174">Proje kökünde *webpack.config.js*adlı bir dosya oluşturun ve aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="f35ef-175">Önceki dosya Webpack derlemesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="f35ef-176">Dikkat edilmesi gereken bazı yapılandırma ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="f35ef-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="f35ef-177">`output` Özellik, *dist'in*varsayılan değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="f35ef-178">Paket bunun yerine *wwwroot* dizininde yayılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="f35ef-179">Dizi `resolve.extensions` SignalR istemcijavascript almak için *.js* içerir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="f35ef-180">Projenin istemci tarafındaki varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="f35ef-181">Aşağıdaki biçimlendirmeyle *src/index.html* oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="f35ef-182">Önceki HTML, ana sayfanın ortak biçimlendirmesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="f35ef-183">Yeni bir *src/css* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="f35ef-184">Amacı projenin *.css* dosyalarını depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="f35ef-185">Aşağıdaki CSS ile *src/css/main.css* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="f35ef-186">Önceki *main.css* dosyası uygulama stilleri.</span><span class="sxs-lookup"><span data-stu-id="f35ef-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="f35ef-187">Aşağıdaki JSON ile *src/tsconfig.json* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="f35ef-188">Önceki kod [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretmek için TypeScript derleyicisi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="f35ef-189">Aşağıdaki kodla *src/index.ts* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="f35ef-190">Önceki TypeScript, DOM öğelerine başvuru alır ve iki olay işleyicisi bağlar:</span><span class="sxs-lookup"><span data-stu-id="f35ef-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="f35ef-191">`keyup`: Kullanıcı `tbMessage`textbox'ta yazdığında bu olay ateşler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="f35ef-192">Kullanıcı `send` **Enter** tuşuna bastığında işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="f35ef-193">`click`: Kullanıcı **Gönder** düğmesini tıklattığında bu olay ateşler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="f35ef-194">`send` işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="f35ef-195">Uygulamayı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f35ef-195">Configure the app</span></span>

1. <span data-ttu-id="f35ef-196">In `Startup.Configure`, [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)aramaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="f35ef-197">Önceki kod, sunucunun *index.html* dosyasını bulmasını ve sunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="f35ef-198">Dosya, kullanıcının tam URL'sini veya web uygulamasının kök URL'sini girip girmediği sunulur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="f35ef-199">Sonunda, hub'a giden bir */hub* rotasını haritala. `ChatHub` `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="f35ef-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="f35ef-200">Hello World'u görüntüleyen kodu *değiştirin!*</span><span class="sxs-lookup"><span data-stu-id="f35ef-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="f35ef-201">aşağıdaki satırla:</span><span class="sxs-lookup"><span data-stu-id="f35ef-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="f35ef-202">In `Startup.ConfigureServices`, [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)arayın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="f35ef-203">SignalR hub'ını depolamak için project root *SignalRWebPack/'de* *Hub adı* verilen yeni bir dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="f35ef-204">Hub *Hub'larını/ChatHub.c'lerini* aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="f35ef-205">Başvuruyu `using` çözmek için Startup.cs dosyasının üst kısmındaaşağıdaki deyimi ekleyin: *Startup.cs* `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="f35ef-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="f35ef-206">İstemci ve sunucu iletişimini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f35ef-206">Enable client and server communication</span></span>

<span data-ttu-id="f35ef-207">Uygulama şu anda mesaj göndermek için temel bir form görüntüler, ancak henüz işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="f35ef-208">Sunucu belirli bir rotayı dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.</span><span class="sxs-lookup"><span data-stu-id="f35ef-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="f35ef-209">Proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="f35ef-210">Önceki komut yükler:</span><span class="sxs-lookup"><span data-stu-id="f35ef-210">The preceding command installs:</span></span>

     * <span data-ttu-id="f35ef-211">İstemcinin sunucuya ileti göndermesine olanak tanıyan [SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr)istemcisi.</span><span class="sxs-lookup"><span data-stu-id="f35ef-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="f35ef-212">Düğüm.js türlerinin derleme zamanı denetimini sağlayan Düğüm.js için TypeScript türü tanımları.</span><span class="sxs-lookup"><span data-stu-id="f35ef-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="f35ef-213">Vurgulanan kodu *src/index.ts* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="f35ef-214">Önceki kod sunucudan ileti almayı destekler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="f35ef-215">Sınıf, `HubConnectionBuilder` sunucu bağlantısını yapılandırmak için yeni bir oluşturucu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="f35ef-216">İşlev `withUrl` hub URL'sini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="f35ef-217">SignalR, istemci ve sunucu arasında ileti alışverişini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="f35ef-218">Her iletinin belirli bir adı vardır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-218">Each message has a specific name.</span></span> <span data-ttu-id="f35ef-219">Örneğin, ada `messageReceived` sahip iletiler, iletiler bölgesinde yeni iletinin görüntülenmesinden sorumlu mantığı çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="f35ef-220">Belirli bir iletiyi dinleme `on` işlevi üzerinden yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="f35ef-221">Herhangi bir sayıda ileti adı dinlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="f35ef-222">Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye aktarmak da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="f35ef-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="f35ef-223">İstemci bir ileti aldıktan `div` sonra, yazarın adı ve özniteliğiyle `innerHTML` ileti içeriğiyle yeni bir öğe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="f35ef-224">İletileri görüntüleyen ana `div` öğeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="f35ef-225">Artık istemci bir ileti alabildiği için, ileti gönderecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="f35ef-226">Vurgulanan kodu *src/index.ts* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="f35ef-227">WebSockets bağlantısı üzerinden ileti göndermek, `send` yöntemi çağırmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="f35ef-228">Yöntemin ilk parametresi ileti adıdır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="f35ef-229">İleti verileri diğer parametrelerde yaşar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="f35ef-230">Bu örnekte, sunucuya `newMessage` olarak tanımlanan bir ileti gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="f35ef-231">İleti, bir metin kutusundan kullanıcı adı ve kullanıcı girişi oluşur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="f35ef-232">Gönderme çalışıyorsa, metin kutusu değeri temizlenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="f35ef-233">`NewMessage` yönetimini `ChatHub` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="f35ef-234">Önceki kod yayınları, sunucu bunları aldıktan sonra bağlı tüm kullanıcılara iletiler aldı.</span><span class="sxs-lookup"><span data-stu-id="f35ef-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="f35ef-235">Tüm iletileri almak için `on` genel bir yöntemolması gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="f35ef-236">İleti adının adını taşıyan bir yöntem yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="f35ef-237">Bu örnekte, TypeScript istemcisi `newMessage`olarak tanımlanan bir ileti gönderir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="f35ef-238">C# `NewMessage` yöntemi istemci tarafından gönderilen verileri bekler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="f35ef-239">Bir arama [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [için Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)yapılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="f35ef-240">Alınan iletiler hub'a bağlı tüm istemcilere gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="f35ef-241">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f35ef-241">Test the app</span></span>

<span data-ttu-id="f35ef-242">Uygulamanın aşağıdaki adımlarla çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f35ef-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f35ef-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f35ef-244">Webpack'i *sürüm* modunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="f35ef-245">Paket **Yöneticisi Konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="f35ef-246">Proje kökünde değilseniz, komutu girmeden önce girin. `cd SignalRWebPack`</span><span class="sxs-lookup"><span data-stu-id="f35ef-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="f35ef-247">Hata ayıklama aracısını takmadan uygulamayı tarayıcıda başlatmak için**hata ayıklama yapmadan** Hata **Ayıklama** > Başlat'ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="f35ef-248">*wwwroot/index.html* dosyası ' `http://localhost:<port_number>`nda servis edilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="f35ef-249">Derleme hataları alırsanız, çözümü kapatmayı ve yeniden açmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="f35ef-250">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="f35ef-251">URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f35ef-252">Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="f35ef-253">Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f35ef-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f35ef-255">Proje kökünde aşağıdaki komutu çalıştırarak Webpack'i *sürüm* modunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="f35ef-256">Proje kökünde aşağıdaki komutu uygulayarak uygulamayı oluşturun ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="f35ef-257">Web sunucusu uygulamayı başlatır ve localhost'ta kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="f35ef-258">Bir tarayıcı `http://localhost:<port_number>`yı aç.</span><span class="sxs-lookup"><span data-stu-id="f35ef-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="f35ef-259">*wwwroot/index.html* dosyası servis edilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="f35ef-260">URL'yi adres çubuğundan kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="f35ef-261">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="f35ef-262">URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f35ef-263">Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="f35ef-264">Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![her iki tarayıcı penceresinde görüntülenen ileti](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="f35ef-266">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f35ef-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f35ef-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f35ef-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f35ef-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile</span><span class="sxs-lookup"><span data-stu-id="f35ef-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="f35ef-269">.NET Core SDK 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="f35ef-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="f35ef-270">[Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="f35ef-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f35ef-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="f35ef-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="f35ef-273">.NET Core SDK 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="f35ef-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="f35ef-274">Visual Studio Code sürüm 1.17.1 veya sonrası için C#</span><span class="sxs-lookup"><span data-stu-id="f35ef-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="f35ef-275">[Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="f35ef-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="f35ef-276">ASP.NET Core web uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f35ef-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f35ef-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f35ef-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f35ef-278">Visual Studio'yu *PATH* ortamı değişkeninde npm'yi arayacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="f35ef-279">Varsayılan olarak, Visual Studio yükleme dizininde bulunan npm sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="f35ef-280">Visual Studio'da şu talimatları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="f35ef-281">**Araçlar** > **Seçenekleri** > **Projeler ve Çözümler** > **Web Paket Yönetimi** > **Dış Web Araçları**gidin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="f35ef-282">Listeden *$(PATH)* girişini seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="f35ef-283">Girişi listedeki ikinci konuma taşımak için yukarı ok'u tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Görsel Stüdyo Yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="f35ef-285">Visual Studio yapılandırması tamamlandı.</span><span class="sxs-lookup"><span data-stu-id="f35ef-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="f35ef-286">Projeyi oluşturma nın zamanı.</span><span class="sxs-lookup"><span data-stu-id="f35ef-286">It's time to create the project.</span></span>

1. <span data-ttu-id="f35ef-287">> **New** Yeni > **Proje Dosyası** menüsü seçeneğini kullanın ve **ASP.NET Çekirdek Web Uygulaması** şablonuna gidin. **Project**</span><span class="sxs-lookup"><span data-stu-id="f35ef-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="f35ef-288">Projeye *SignalRWebPack*adını ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="f35ef-289">Hedef çerçeve açılır tarafından *.NET Core'u* seçin ve çerçeve seçici açılır ASP.NET *Core 2.2'yi* seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="f35ef-290">Boş **şablonu** seçin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f35ef-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f35ef-292">**Entegre Terminal'de**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="f35ef-293">.NET Core'u hedefleyen boş bir ASP.NET Core web uygulaması *SignalRWebPack* dizininde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="f35ef-294">Webpack ve TypeScript yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f35ef-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="f35ef-295">Aşağıdaki adımlar, TypeScript'in JavaScript'e dönüştürülmesini ve istemci tarafındaki kaynakların birbirlemasını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="f35ef-296">*Package.json* dosyası oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="f35ef-297">Vurgulanan özelliği *package.json* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="f35ef-298">`private` Bir sonraki `true` adımda paket yükleme uyarılarını engelleyecek şekilde özelliği ayarlama.</span><span class="sxs-lookup"><span data-stu-id="f35ef-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="f35ef-299">Gerekli npm paketlerini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-299">Install the required npm packages.</span></span> <span data-ttu-id="f35ef-300">Proje kökünden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="f35ef-301">Dikkat edilmesi gereken bazı komut ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="f35ef-301">Some command details to note:</span></span>

    * <span data-ttu-id="f35ef-302">Her paket adı `@` için bir sürüm numarası işareti izler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="f35ef-303">npm bu özel paket sürümlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="f35ef-304">Seçenek, `-E` npm'nin [semantik sürüm](https://semver.org/) aralığı işleçlerini *package.json'a*yazma varsayılan davranışını devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="f35ef-305">Örneğin, `"webpack": "4.29.3"` `"webpack": "^4.29.3"`".</span><span class="sxs-lookup"><span data-stu-id="f35ef-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="f35ef-306">Bu seçenek, yeni paket sürümlerinde istenmeyen yükseltmeleri önler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="f35ef-307">Daha fazla ayrıntı için [npm-install](https://docs.npmjs.com/cli/install) dokümanlarına bakın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="f35ef-308">`scripts` *Package.json* dosyasının özelliğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="f35ef-309">Komut dosyalarının bazı açıklamaları:</span><span class="sxs-lookup"><span data-stu-id="f35ef-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="f35ef-310">`build`: İstemci tarafındaki kaynakları geliştirme modunda paketler ve dosya değişikliklerini izler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="f35ef-311">Dosya izleyicisi, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="f35ef-312">Bu `mode` seçenek, ağaç sallayarak ve minification gibi üretim optimizasyonları devre dışı bırakmaz.</span><span class="sxs-lookup"><span data-stu-id="f35ef-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="f35ef-313">Yalnızca `build` geliştirmede kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="f35ef-314">`release`: İstemci tarafındaki kaynakları üretim modunda paketler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="f35ef-315">`publish`: İstemci tarafındaki kaynakları üretim modunda bir araya getirmek için komut dosyasını `release` çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="f35ef-316">.NET Core CLI'nin uygulamayı yayımlamak için [yayımlama](/dotnet/core/tools/dotnet-publish) komutunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="f35ef-317">Proje kökünde *webpack.config.js* adlı bir dosya oluşturun ve aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="f35ef-318">Önceki dosya Webpack derlemesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="f35ef-319">Dikkat edilmesi gereken bazı yapılandırma ayrıntıları:</span><span class="sxs-lookup"><span data-stu-id="f35ef-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="f35ef-320">`output` Özellik, *dist'in*varsayılan değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="f35ef-321">Paket bunun yerine *wwwroot* dizininde yayılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="f35ef-322">Dizi `resolve.extensions` SignalR istemcijavascript almak için *.js* içerir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="f35ef-323">Projenin istemci tarafındaki varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="f35ef-324">Aşağıdaki biçimlendirmeyle *src/index.html* oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="f35ef-325">Önceki HTML, ana sayfanın ortak biçimlendirmesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="f35ef-326">Yeni bir *src/css* dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="f35ef-327">Amacı projenin *.css* dosyalarını depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="f35ef-328">Aşağıdaki biçimlendirme ile *src/css/main.css* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="f35ef-329">Önceki *main.css* dosyası uygulama stilleri.</span><span class="sxs-lookup"><span data-stu-id="f35ef-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="f35ef-330">Aşağıdaki JSON ile *src/tsconfig.json* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="f35ef-331">Önceki kod [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretmek için TypeScript derleyicisi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="f35ef-332">Aşağıdaki kodla *src/index.ts* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="f35ef-333">Önceki TypeScript, DOM öğelerine başvuru alır ve iki olay işleyicisi bağlar:</span><span class="sxs-lookup"><span data-stu-id="f35ef-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="f35ef-334">`keyup`: Kullanıcı `tbMessage` textbox'ta yazdığında bu olay ateşler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="f35ef-335">Kullanıcı `send` **Enter** tuşuna bastığında işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="f35ef-336">`click`: Kullanıcı **Gönder** düğmesini tıklattığında bu olay ateşler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="f35ef-337">`send` işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="f35ef-338">ASP.NET Core uygulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f35ef-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="f35ef-339">`Startup.Configure` Yöntemde sağlanan kod *Hello World görüntüler!*.</span><span class="sxs-lookup"><span data-stu-id="f35ef-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="f35ef-340">Yöntem `app.Run` çağrısını [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağrılarıyla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="f35ef-341">Önceki kod, kullanıcının tam URL'sini veya web uygulamasının kök URL'sini girip girmediğini sunucunun *index.html* dosyasını bulup sunmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="f35ef-342">[AddSignalR'ı](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'da `Startup.ConfigureServices`arayın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f35ef-343">Projeye SignalR hizmetlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="f35ef-344">Hub'a bir */hub* rotasını eşle. `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="f35ef-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="f35ef-345">Sonunda aşağıdaki satırları `Startup.Configure`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="f35ef-346">Proje kökünde Hub *adı*verilen yeni bir dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f35ef-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="f35ef-347">Amacı, bir sonraki adımda oluşturulan SignalR hub'ını depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="f35ef-348">Hub *Hub'larını/ChatHub.c'lerini* aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f35ef-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="f35ef-349">Başvuruyu çözmek için Startup.cs dosyanın üst kısmındaaşağıdaki kodu ekleyin: *Startup.cs* `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="f35ef-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="f35ef-350">İstemci ve sunucu iletişimini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f35ef-350">Enable client and server communication</span></span>

<span data-ttu-id="f35ef-351">Uygulama şu anda mesaj göndermek için basit bir form görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="f35ef-352">Bunu yapmaya çalıştığın zaman hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="f35ef-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="f35ef-353">Sunucu belirli bir rotayı dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.</span><span class="sxs-lookup"><span data-stu-id="f35ef-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="f35ef-354">Proje kökünde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="f35ef-355">Önceki komut, istemcinin sunucuya ileti göndermesine olanak tanıyan [SignalR TypeScript istemcisini](https://www.npmjs.com/package/@microsoft/signalr)yükler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="f35ef-356">Vurgulanan kodu *src/index.ts* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="f35ef-357">Önceki kod sunucudan ileti almayı destekler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="f35ef-358">Sınıf, `HubConnectionBuilder` sunucu bağlantısını yapılandırmak için yeni bir oluşturucu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="f35ef-359">İşlev `withUrl` hub URL'sini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="f35ef-360">SignalR, istemci ve sunucu arasında ileti alışverişini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="f35ef-361">Her iletinin belirli bir adı vardır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-361">Each message has a specific name.</span></span> <span data-ttu-id="f35ef-362">Örneğin, ada `messageReceived` sahip iletiler, iletiler bölgesinde yeni iletinin görüntülenmesinden sorumlu mantığı çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="f35ef-363">Belirli bir iletiyi dinleme `on` işlevi üzerinden yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="f35ef-364">İstediğiniz sayıda ileti adını dinleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f35ef-364">You can listen to any number of message names.</span></span> <span data-ttu-id="f35ef-365">Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye aktarmak da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="f35ef-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="f35ef-366">İstemci bir ileti aldıktan `div` sonra, yazarın adı ve özniteliğiyle `innerHTML` ileti içeriğiyle yeni bir öğe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="f35ef-367">Yeni ileti, iletileri `div` görüntüleyen ana öğeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="f35ef-368">Artık istemci bir ileti alabildiği için, ileti gönderecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="f35ef-369">Vurgulanan kodu *src/index.ts* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="f35ef-370">WebSockets bağlantısı üzerinden ileti göndermek, `send` yöntemi çağırmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="f35ef-371">Yöntemin ilk parametresi ileti adıdır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="f35ef-372">İleti verileri diğer parametrelerde yaşar.</span><span class="sxs-lookup"><span data-stu-id="f35ef-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="f35ef-373">Bu örnekte, sunucuya `newMessage` olarak tanımlanan bir ileti gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="f35ef-374">İleti, bir metin kutusundan kullanıcı adı ve kullanıcı girişi oluşur.</span><span class="sxs-lookup"><span data-stu-id="f35ef-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="f35ef-375">Gönderme çalışıyorsa, metin kutusu değeri temizlenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="f35ef-376">`NewMessage` yönetimini `ChatHub` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f35ef-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="f35ef-377">Önceki kod yayınları, sunucu bunları aldıktan sonra bağlı tüm kullanıcılara iletiler aldı.</span><span class="sxs-lookup"><span data-stu-id="f35ef-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="f35ef-378">Tüm iletileri almak için `on` genel bir yöntemolması gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="f35ef-379">İleti adının adını taşıyan bir yöntem yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="f35ef-380">Bu örnekte, TypeScript istemcisi `newMessage`olarak tanımlanan bir ileti gönderir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="f35ef-381">C# `NewMessage` yöntemi istemci tarafından gönderilen verileri bekler.</span><span class="sxs-lookup"><span data-stu-id="f35ef-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="f35ef-382">Bir arama [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [için Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)yapılır.</span><span class="sxs-lookup"><span data-stu-id="f35ef-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="f35ef-383">Alınan iletiler hub'a bağlı tüm istemcilere gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="f35ef-384">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f35ef-384">Test the app</span></span>

<span data-ttu-id="f35ef-385">Uygulamanın aşağıdaki adımlarla çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f35ef-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f35ef-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f35ef-387">Webpack'i *sürüm* modunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="f35ef-388">Paket **Yöneticisi Konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="f35ef-389">Proje kökünde değilseniz, komutu girmeden önce girin. `cd SignalRWebPack`</span><span class="sxs-lookup"><span data-stu-id="f35ef-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="f35ef-390">Hata ayıklama aracısını takmadan uygulamayı tarayıcıda başlatmak için**hata ayıklama yapmadan** Hata **Ayıklama** > Başlat'ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f35ef-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="f35ef-391">*wwwroot/index.html* dosyası ' `http://localhost:<port_number>`nda servis edilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="f35ef-392">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="f35ef-393">URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f35ef-394">Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="f35ef-395">Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f35ef-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f35ef-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f35ef-397">Proje kökünde aşağıdaki komutu çalıştırarak Webpack'i *sürüm* modunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="f35ef-398">Proje kökünde aşağıdaki komutu uygulayarak uygulamayı oluşturun ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f35ef-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="f35ef-399">Web sunucusu uygulamayı başlatır ve localhost'ta kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="f35ef-400">Bir tarayıcı `http://localhost:<port_number>`yı aç.</span><span class="sxs-lookup"><span data-stu-id="f35ef-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="f35ef-401">*wwwroot/index.html* dosyası servis edilir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="f35ef-402">URL'yi adres çubuğundan kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="f35ef-403">Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="f35ef-404">URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f35ef-405">Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f35ef-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="f35ef-406">Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f35ef-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![her iki tarayıcı penceresinde görüntülenen ileti](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f35ef-408">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f35ef-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
