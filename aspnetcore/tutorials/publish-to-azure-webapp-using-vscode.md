---
title: Visual Studio Code ile Azure 'da ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: Visual Studio Code kullanarak Azure App Service ASP.NET Core uygulama yayımlamayı öğrenin
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 28074d64e9d4d0ec35af06e8b6daae939e316b2b
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130216"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="c8cda-103">Visual Studio Code ile Azure 'da ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="c8cda-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="c8cda-104">[Ricardo Serrat](https://twitter.com/ricardoserradas) tarafından</span><span class="sxs-lookup"><span data-stu-id="c8cda-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="c8cda-105">App Service dağıtım sorununu gidermek için, bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="c8cda-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="c8cda-106">Giriş</span><span class="sxs-lookup"><span data-stu-id="c8cda-106">Intro</span></span>

<span data-ttu-id="c8cda-107">Bu öğreticide, ASP.Net Core MVC uygulaması oluşturmayı ve Visual Studio Code içinde dağıtmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="c8cda-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="c8cda-108">Kurulum</span><span class="sxs-lookup"><span data-stu-id="c8cda-108">Set up</span></span>

- <span data-ttu-id="c8cda-109">Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.</span><span class="sxs-lookup"><span data-stu-id="c8cda-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="c8cda-110">[.NET Core SDK](https://dotnet.microsoft.com/download) yüklensin</span><span class="sxs-lookup"><span data-stu-id="c8cda-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="c8cda-111">[Visual Studio Code](https://code.visualstudio.com/Download) yüklensin</span><span class="sxs-lookup"><span data-stu-id="c8cda-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="c8cda-112">Visual Studio Code için [C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) yükler</span><span class="sxs-lookup"><span data-stu-id="c8cda-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="c8cda-113">Devam etmeden önce Visual Studio Code [Azure App Service uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) yükleyip yapılandırmak için</span><span class="sxs-lookup"><span data-stu-id="c8cda-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="c8cda-114">ASP.Net Core MVC projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="c8cda-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="c8cda-115">Bir Terminal kullanarak, projenin oluşturulmasını istediğiniz klasöre gidin ve aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c8cda-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="c8cda-116">Aşağıdakine benzer bir klasör yapısına sahip olacaksınız:</span><span class="sxs-lookup"><span data-stu-id="c8cda-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="c8cda-117">Visual Studio Code ile açın</span><span class="sxs-lookup"><span data-stu-id="c8cda-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="c8cda-118">Projeniz oluşturulduktan sonra, aşağıdaki seçeneklerden birini kullanarak Visual Studio Code ile açabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c8cda-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="c8cda-119">Komut satırı aracılığıyla</span><span class="sxs-lookup"><span data-stu-id="c8cda-119">Through the command line</span></span>

<span data-ttu-id="c8cda-120">Projeyi oluşturduğunuz klasör içinde aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c8cda-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="c8cda-121">Aşağıdaki komut çalışmazsa, [Bu bağlantıya](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)başvurarak yüklemenizin doğru şekilde yapılandırılıp yapılandırılmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="c8cda-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="c8cda-122">Visual Studio Code arabirimi aracılığıyla</span><span class="sxs-lookup"><span data-stu-id="c8cda-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="c8cda-123">Visual Studio Code’u açın</span><span class="sxs-lookup"><span data-stu-id="c8cda-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="c8cda-124">Menüsünde, şunu seçin`File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="c8cda-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="c8cda-125">MVC projesini oluşturduğunuz klasörün kökünü seçin</span><span class="sxs-lookup"><span data-stu-id="c8cda-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="c8cda-126">Proje klasörünü açtığınızda, gerekli varlıkların derleme ve hata ayıklama için eksik olduğunu söyleyen bir ileti alırsınız.</span><span class="sxs-lookup"><span data-stu-id="c8cda-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="c8cda-127">Bunları eklemek için yardımı kabul edin.</span><span class="sxs-lookup"><span data-stu-id="c8cda-127">Accept the help to add them.</span></span>

![Project yüklü Visual Studio Code arabirimi](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="c8cda-129">`.vscode`Proje yapısı altında bir klasör oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c8cda-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="c8cda-130">Bu, aşağıdaki dosyaları içerir:</span><span class="sxs-lookup"><span data-stu-id="c8cda-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="c8cda-131">Bunlar, .NET Core Web uygulamanızı oluşturmanıza ve hata ayıklamanıza yardımcı olan yardımcı dosyalardır.</span><span class="sxs-lookup"><span data-stu-id="c8cda-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="c8cda-132">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c8cda-132">Run the app</span></span>

<span data-ttu-id="c8cda-133">Uygulamayı Azure 'a dağıtmadan önce, yerel makinenizde düzgün çalıştığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="c8cda-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="c8cda-134">Projeyi çalıştırmak için F5 'e basın</span><span class="sxs-lookup"><span data-stu-id="c8cda-134">Press F5 to run the project</span></span>

<span data-ttu-id="c8cda-135">Web uygulamanız, varsayılan tarayıcınızın yeni bir sekmesinde çalışmaya başlayacaktır.</span><span class="sxs-lookup"><span data-stu-id="c8cda-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="c8cda-136">Başladıktan hemen sonra bir gizlilik uyarısı görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c8cda-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="c8cda-137">Bunun nedeni, uygulamanızın HTTP ve HTTPS kullanılarak başlayacağından ve varsayılan olarak HTTPS uç noktasına gider.</span><span class="sxs-lookup"><span data-stu-id="c8cda-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Uygulamanın yerel olarak hata ayıklaması sırasında Gizlilik Uyarısı](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="c8cda-139">Hata ayıklama oturumunu tutmak için `Advanced` ve ardından ' a tıklayın `Continue to localhost (unsafe)` .</span><span class="sxs-lookup"><span data-stu-id="c8cda-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="c8cda-140">Dağıtım paketini yerel olarak oluşturma</span><span class="sxs-lookup"><span data-stu-id="c8cda-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="c8cda-141">Visual Studio Code terminali aç</span><span class="sxs-lookup"><span data-stu-id="c8cda-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="c8cda-142">`Release`Adlı bir alt klasöre paket oluşturmak için aşağıdaki komutu kullanın `publish` :</span><span class="sxs-lookup"><span data-stu-id="c8cda-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="c8cda-143">`publish`Proje yapısı altında yeni bir klasör oluşturulacak</span><span class="sxs-lookup"><span data-stu-id="c8cda-143">A new `publish` folder will be created under the project structure</span></span>

![Klasör yapısını Yayımla](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="c8cda-145">Azure App Service’e yayımlama</span><span class="sxs-lookup"><span data-stu-id="c8cda-145">Publish to Azure App Service</span></span>

<span data-ttu-id="c8cda-146">Visual Studio Code Azure App Service uzantısı 'ndan yararlanarak, Web sitesini doğrudan Azure App Service yayımlamak için aşağıdaki adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="c8cda-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="c8cda-147">Yeni bir Web uygulaması oluşturuyorsanız</span><span class="sxs-lookup"><span data-stu-id="c8cda-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="c8cda-148">Klasöre sağ tıklayın `publish` ve şunları seçin`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="c8cda-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="c8cda-149">Web uygulaması oluşturmak istediğiniz aboneliği seçin</span><span class="sxs-lookup"><span data-stu-id="c8cda-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="c8cda-150">`Create New Web App` seçeneğini belirleyin</span><span class="sxs-lookup"><span data-stu-id="c8cda-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="c8cda-151">Web uygulaması için bir ad girin</span><span class="sxs-lookup"><span data-stu-id="c8cda-151">Enter a name for the Web App</span></span>

<span data-ttu-id="c8cda-152">Uzantı yeni Web uygulamasını oluşturur ve paketin otomatik olarak dağıtıma başlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c8cda-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="c8cda-153">Dağıtım tamamlandıktan sonra, `Browse Website` dağıtımı doğrulamak için öğesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c8cda-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Dağıtım başarılı iletisi](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="c8cda-155">' A tıkladığınızda `Browse Website` , varsayılan tarayıcınızı kullanarak bu sayfaya gidebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c8cda-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Yeni Web uygulaması başarıyla dağıtıldı](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="c8cda-157">Var olan bir Web uygulamasına dağıtım yapıyorsanız</span><span class="sxs-lookup"><span data-stu-id="c8cda-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="c8cda-158">Klasöre sağ tıklayın `publish` ve şunları seçin`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="c8cda-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="c8cda-159">Mevcut Web uygulamasının bulunduğu aboneliği seçin</span><span class="sxs-lookup"><span data-stu-id="c8cda-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="c8cda-160">Listeden Web uygulamasını seçin</span><span class="sxs-lookup"><span data-stu-id="c8cda-160">Select the Web App from the list</span></span>
- <span data-ttu-id="c8cda-161">Visual Studio Code, var olan içeriğin üzerine yazmak isteyip istemediğinizi sorar.</span><span class="sxs-lookup"><span data-stu-id="c8cda-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="c8cda-162">`Deploy`Onaylamak için tıklayın</span><span class="sxs-lookup"><span data-stu-id="c8cda-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="c8cda-163">Uzantı, güncelleştirilmiş içeriği Web uygulamasına dağıtacaktır.</span><span class="sxs-lookup"><span data-stu-id="c8cda-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="c8cda-164">Tamamlandıktan sonra, `Browse Website` dağıtımı doğrulamak için öğesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c8cda-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Mevcut Web uygulaması başarıyla dağıtıldı](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="c8cda-166">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="c8cda-166">Next steps</span></span>

- [<span data-ttu-id="c8cda-167">İlk Azure DevOps işlem hattınızı oluşturma</span><span class="sxs-lookup"><span data-stu-id="c8cda-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="c8cda-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c8cda-168">Additional resources</span></span>

- [<span data-ttu-id="c8cda-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c8cda-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="c8cda-170">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="c8cda-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
