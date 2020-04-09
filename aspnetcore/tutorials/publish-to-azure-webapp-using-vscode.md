---
title: Visual Studio Code ile Azure'da ASP.NET Core uygulaması yayınlama
author: rick-anderson
description: Visual Studio Code'u kullanarak Azure Uygulama Hizmeti'nde ASP.NET Core uygulamasını nasıl yayınlayacağınızı öğrenin
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 5f117cb2867a6e7b54269ef39abe819256b429ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242684"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="9f60f-103">Visual Studio Code ile Azure'da ASP.NET Core uygulaması yayınlama</span><span class="sxs-lookup"><span data-stu-id="9f60f-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="9f60f-104">Yazar: [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="9f60f-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="9f60f-105">Bir Uygulama Hizmeti dağıtım sorununu <xref:test/troubleshoot-azure-iis>gidermek için bkz.</span><span class="sxs-lookup"><span data-stu-id="9f60f-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="9f60f-106">Giriş</span><span class="sxs-lookup"><span data-stu-id="9f60f-106">Intro</span></span>

<span data-ttu-id="9f60f-107">Bu öğretici ile, nasıl bir ASP.Net Core MVC Uygulama oluşturmak ve Visual Studio Code içinde dağıtmak öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="9f60f-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="9f60f-108">Ayarla</span><span class="sxs-lookup"><span data-stu-id="9f60f-108">Set up</span></span>

- <span data-ttu-id="9f60f-109">Hesabınız yoksa ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.</span><span class="sxs-lookup"><span data-stu-id="9f60f-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="9f60f-110">[Install .NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="9f60f-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="9f60f-111">[Visual Studio Kodunu](https://code.visualstudio.com/Download) Yükleyin</span><span class="sxs-lookup"><span data-stu-id="9f60f-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="9f60f-112">[C# Uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Visual Studio Koduna Yükleyin</span><span class="sxs-lookup"><span data-stu-id="9f60f-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="9f60f-113">Azure [Uygulama Hizmeti Uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Visual Studio Koduna yükleyin ve devam etmeden önce yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9f60f-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="9f60f-114">ASP.Net Core MVC projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="9f60f-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="9f60f-115">Bir terminal kullanarak, projenin oluşturulmasını istediğiniz klasöre gidin ve aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="9f60f-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="9f60f-116">Aşağıdakilere benzer bir klasör yapınız olacak:</span><span class="sxs-lookup"><span data-stu-id="9f60f-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="9f60f-117">Visual Studio Code ile açın</span><span class="sxs-lookup"><span data-stu-id="9f60f-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="9f60f-118">Projeniz oluşturulduktan sonra aşağıdaki seçeneklerden birini kullanarak Visual Studio Code ile açabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="9f60f-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="9f60f-119">Komut satırı ndan</span><span class="sxs-lookup"><span data-stu-id="9f60f-119">Through the command line</span></span>

<span data-ttu-id="9f60f-120">Projeyi oluşturduğunuz klasörde aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="9f60f-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="9f60f-121">Aşağıdaki komut çalışmıyorsa, [yüklemenizin bu bağlantıya](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)başvurarak düzgün bir şekilde yapılandırıp yapılmadığını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="9f60f-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="9f60f-122">Visual Studio Code arabirimi ile</span><span class="sxs-lookup"><span data-stu-id="9f60f-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="9f60f-123">Görsel Stüdyo Kodunu Aç</span><span class="sxs-lookup"><span data-stu-id="9f60f-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="9f60f-124">Menüde,`File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="9f60f-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="9f60f-125">MVC Projesi'ni oluşturduğunuz klasörün kökünü seçin</span><span class="sxs-lookup"><span data-stu-id="9f60f-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="9f60f-126">Proje klasörünü açtığınızda, oluşturmak ve hata ayıklamak için gerekli varlıkların eksik olduğunu belirten bir ileti alırsınız.</span><span class="sxs-lookup"><span data-stu-id="9f60f-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="9f60f-127">Bunları eklemek için yardım kabul edin.</span><span class="sxs-lookup"><span data-stu-id="9f60f-127">Accept the help to add them.</span></span>

![Proje yüklü Görsel Stüdyo Kodu arayüzü](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="9f60f-129">Proje `.vscode` yapısının altında bir klasör oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9f60f-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="9f60f-130">Aşağıdaki dosyaları içerecektir:</span><span class="sxs-lookup"><span data-stu-id="9f60f-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="9f60f-131">Bunlar,.NET Core Web Uygulamanızı oluşturmanıza ve hata ayıklamanıza yardımcı olacak yardımcı program dosyalarıdır.</span><span class="sxs-lookup"><span data-stu-id="9f60f-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="9f60f-132">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="9f60f-132">Run the app</span></span>

<span data-ttu-id="9f60f-133">Uygulamayı Azure'a dağıtmadan önce, uygulamanın yerel makinenizde düzgün şekilde çalıştırdığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="9f60f-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="9f60f-134">Projeyi çalıştırmak için F5 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="9f60f-134">Press F5 to run the project</span></span>

<span data-ttu-id="9f60f-135">Web uygulamanız varsayılan tarayıcınızın yeni bir sekmesinde çalışmaya başlar.</span><span class="sxs-lookup"><span data-stu-id="9f60f-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="9f60f-136">Başlar başlamaz bir gizlilik uyarısı fark edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9f60f-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="9f60f-137">Bunun nedeni, uygulamanızın HTTP ve HTTPS'yi kullanmaya başlaması ve varsayılan olarak HTTPS bitiş noktasına yönlendirilen uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="9f60f-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Uygulamayı yerel olarak hata ayıklarken gizlilik uyarısı](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="9f60f-139">Hata ayıklama oturumunu tutmak `Advanced` için `Continue to localhost (unsafe)`tıklatın ve sonra.</span><span class="sxs-lookup"><span data-stu-id="9f60f-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="9f60f-140">Dağıtım paketini yerel olarak oluşturma</span><span class="sxs-lookup"><span data-stu-id="9f60f-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="9f60f-141">Açık Görsel Stüdyo Kodu terminali</span><span class="sxs-lookup"><span data-stu-id="9f60f-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="9f60f-142">Bir `publish`alt klasöre `Release` bir paket oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="9f60f-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="9f60f-143">Proje `publish` yapısı altında yeni bir klasör oluşturulacak</span><span class="sxs-lookup"><span data-stu-id="9f60f-143">A new `publish` folder will be created under the project structure</span></span>

![Klasör yapısını yayımlama](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="9f60f-145">Azure App Service’e yayımlama</span><span class="sxs-lookup"><span data-stu-id="9f60f-145">Publish to Azure App Service</span></span>

<span data-ttu-id="9f60f-146">Visual Studio Code için Azure Uygulama Hizmeti uzantısından yararlanarak, web sitesini doğrudan Azure Uygulama Hizmeti'nde yayınlamak için aşağıdaki adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="9f60f-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="9f60f-147">Yeni bir Web Uygulaması oluşturuyorsanız</span><span class="sxs-lookup"><span data-stu-id="9f60f-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="9f60f-148">Klasöre `publish` sağ tıklayın ve`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="9f60f-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="9f60f-149">Web Uygulamasını oluşturmak istediğiniz aboneliği seçin</span><span class="sxs-lookup"><span data-stu-id="9f60f-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="9f60f-150">`Create New Web App` seçeneğini belirleyin</span><span class="sxs-lookup"><span data-stu-id="9f60f-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="9f60f-151">Web Uygulaması için bir ad girin</span><span class="sxs-lookup"><span data-stu-id="9f60f-151">Enter a name for the Web App</span></span>

<span data-ttu-id="9f60f-152">Uzantı yeni Web Uygulamasını oluşturur ve paketi otomatik olarak dağıtmaya başlar.</span><span class="sxs-lookup"><span data-stu-id="9f60f-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="9f60f-153">Dağıtım tamamlandıktan sonra, `Browse Website` dağıtımı doğrulamak için tıklatın.</span><span class="sxs-lookup"><span data-stu-id="9f60f-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Dağıtım başarılı ileti](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="9f60f-155">Tıklattığınızda, `Browse Website`varsayılan tarayıcınızı kullanarak tarayıcıya gidersiniz:</span><span class="sxs-lookup"><span data-stu-id="9f60f-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Yeni Web Uygulaması başarıyla dağıtıldı](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="9f60f-157">Varolan bir Web Uygulamasına dağıtım yapıyorsunuz</span><span class="sxs-lookup"><span data-stu-id="9f60f-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="9f60f-158">Klasöre `publish` sağ tıklayın ve`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="9f60f-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="9f60f-159">Varolan Web Uygulamasının bulunduğu aboneliği seçin</span><span class="sxs-lookup"><span data-stu-id="9f60f-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="9f60f-160">Listeden Web Uygulamasını seçin</span><span class="sxs-lookup"><span data-stu-id="9f60f-160">Select the Web App from the list</span></span>
- <span data-ttu-id="9f60f-161">Visual Studio Code, varolan içeriğin üzerine yazmak isteyip istemediğinizi sorar.</span><span class="sxs-lookup"><span data-stu-id="9f60f-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="9f60f-162">Onaylamak `Deploy` için tıklayın</span><span class="sxs-lookup"><span data-stu-id="9f60f-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="9f60f-163">Uzantı, güncelleştirilmiş içeriği Web Uygulamasına dağıtacaktır.</span><span class="sxs-lookup"><span data-stu-id="9f60f-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="9f60f-164">Bu iş bittikten `Browse Website` sonra, dağıtımı doğrulamak için tıklatın.</span><span class="sxs-lookup"><span data-stu-id="9f60f-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Mevcut Web Uygulaması başarıyla dağıtıldı](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="9f60f-166">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9f60f-166">Next steps</span></span>

- [<span data-ttu-id="9f60f-167">İlk Azure DevOps ardCihazınızı oluşturun</span><span class="sxs-lookup"><span data-stu-id="9f60f-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="9f60f-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9f60f-168">Additional resources</span></span>

- [<span data-ttu-id="9f60f-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9f60f-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="9f60f-170">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="9f60f-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
