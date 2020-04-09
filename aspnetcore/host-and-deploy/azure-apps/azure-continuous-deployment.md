---
title: ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım
author: rick-anderson
description: Visual Studio'yu kullanarak bir ASP.NET Core web uygulaması oluşturmayı ve sürekli dağıtım için Git'i kullanarak Azure Uygulama Hizmeti'ne nasıl dağıttığı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660855"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="c054f-103">ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım</span><span class="sxs-lookup"><span data-stu-id="c054f-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="c054f-104">Yazar: [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="c054f-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="c054f-105">Bu öğretici, Visual Studio'ASP.NET bir web uygulamasının nasıl oluşturulup Visual Studio'dan Azure Uygulama Hizmeti'ne sürekli dağıtım kullanarak nasıl dağıtılanın gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="c054f-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="c054f-106">Ayrıca Azure DevOps Hizmetlerini kullanarak [Azure Uygulama Hizmeti](/azure/app-service/app-service-web-overview) için sürekli teslimat (CD) iş akışını nasıl yapılandırabileceğinizi gösteren Azure [Ardışık Hatları ile ilk ardışık alanınızı oluşturun.](/azure/devops/pipelines/get-started-yaml)</span><span class="sxs-lookup"><span data-stu-id="c054f-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="c054f-107">Azure Ardışık Hatları (Azure DevOps Hizmetleri hizmeti), Azure Uygulama Hizmeti'nde barındırılan uygulamaların güncelleştirmelerini yayımlamak için sağlam bir dağıtım ardışık birimi oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="c054f-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="c054f-108">Ardışık düzen, Azure portalından, testleri oluşturmak, çalıştırmak, bir hazırlama yuvasına dağıtmak ve sonra üretime dağıtmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c054f-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="c054f-109">Bu öğreticiyi tamamlamak için bir Microsoft Azure hesabı gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c054f-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="c054f-110">Hesap edinmek için [MSDN abone avantajlarını etkinleştirin](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) veya [ücretsiz deneme sürümüne kaydolun.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)</span><span class="sxs-lookup"><span data-stu-id="c054f-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c054f-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c054f-111">Prerequisites</span></span>

<span data-ttu-id="c054f-112">Bu öğretici, aşağıdaki yazılımın yüklü olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="c054f-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="c054f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c054f-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="c054f-114">Windows için [Git](https://git-scm.com/downloads)</span><span class="sxs-lookup"><span data-stu-id="c054f-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="c054f-115">ASP.NET Core web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c054f-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="c054f-116">Visual Studio’yu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c054f-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="c054f-117">**Dosya** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="c054f-118">ASP.NET **Çekirdek Web Uygulaması** proje şablonu seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="c054f-119">**Yüklü** > **Şablonlar** > **Görsel C#** > **.NET Core**altında görünür.</span><span class="sxs-lookup"><span data-stu-id="c054f-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="c054f-120">Projeyi `SampleWebAppDemo`adlandırın.</span><span class="sxs-lookup"><span data-stu-id="c054f-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="c054f-121">Yeni **Git deposu Oluştur** seçeneğini seçin ve **Tamam'ı**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="c054f-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Yeni Proje iletişim kutusu](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="c054f-123">Yeni **ASP.NET Çekirdek Projesi** iletişim kutusunda, ASP.NET Çekirdek **Boş** şablonuna tıklayın ve **ardından Tamam'ı**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="c054f-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Yeni ASP.NET Çekirdek Projesi iletişim kutusu](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="c054f-125">.NET Core'un en son sürümü 2.0'dır.</span><span class="sxs-lookup"><span data-stu-id="c054f-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="c054f-126">Web uygulamasını yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c054f-126">Running the web app locally</span></span>

1. <span data-ttu-id="c054f-127">Visual Studio uygulamayı oluşturmayı bitirdikten sonra **Hata** > **Ayıklama Başlat'ı**seçerek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c054f-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="c054f-128">Alternatif olarak **F5**tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="c054f-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="c054f-129">Visual Studio ve yeni uygulamayı başlatması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="c054f-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="c054f-130">Tamamlandıktan sonra, tarayıcı çalışan uygulamayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="c054f-130">Once it's complete, the browser shows the running app.</span></span>

   !['Hello World!' uygulamasını gösteren tarayıcı penceresi](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="c054f-132">Çalışan Web uygulamasını inceledikten sonra tarayıcıyı kapatın ve uygulamayı durdurmak için Visual Studio'nun araç çubuğundaki "Hata Ayıklamayı Durdur" simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="c054f-133">Azure Portalı'nda bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c054f-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="c054f-134">Aşağıdaki adımlar Azure Portalı'nda bir web uygulaması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c054f-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="c054f-135">[Azure Portalı'na](https://portal.azure.com)giriş yapın.</span><span class="sxs-lookup"><span data-stu-id="c054f-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="c054f-136">Portal arabiriminin sol üst kısmında **Yenİ'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="c054f-137">**Web + Mobil** > **Web Uygulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portalı: Yeni düğme: Web + Mobile under Marketplace: Öne Çıkan Uygulamalar altında Web Uygulaması düğmesi](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="c054f-139">Web **App** blade'ine, Uygulama **Hizmet Adı**için benzersiz bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="c054f-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Web App bıçak](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="c054f-141">**Uygulama Hizmeti Adı** benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c054f-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="c054f-142">Ad sağlandığında portal bu kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="c054f-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="c054f-143">Farklı bir değer sağlıyorsanız, bu öğreticide **SampleWebAppDemo'nun** her oluşumu için bu değeri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c054f-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="c054f-144">**Ayrıca Web App** blade'de, varolan bir Uygulama Hizmet **Planı/Konumu'nu** seçin veya yeni bir uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c054f-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="c054f-145">Yeni bir plan oluşturuyorsanız, fiyatlandırma katmanını, konumu ve diğer seçenekleri seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="c054f-146">Uygulama Hizmeti planları hakkında daha fazla bilgi için Azure [Uygulama Hizmeti planlarına derinlemesine genel bakış](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)bakın.</span><span class="sxs-lookup"><span data-stu-id="c054f-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="c054f-147">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-147">Select **Create**.</span></span> <span data-ttu-id="c054f-148">Azure, web uygulamasını sağlayacak ve başlatacaktır.</span><span class="sxs-lookup"><span data-stu-id="c054f-148">Azure will provision and start the web app.</span></span>

   ![Azure Portalı: Örnek Web App Demo 01 Essentials bıçak](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="c054f-150">Yeni web uygulaması için Git yayımlamayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="c054f-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="c054f-151">Git, bir Azure App Service web uygulamasını dağıtmak için kullanılabilecek dağıtılmış bir sürüm kontrol sistemidir.</span><span class="sxs-lookup"><span data-stu-id="c054f-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="c054f-152">Web uygulaması kodu yerel bir Git deposunda depolanır ve kod uzak bir depoya itilerek Azure'a dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="c054f-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="c054f-153">[Azure Portalına](https://portal.azure.com)giriş yapın.</span><span class="sxs-lookup"><span data-stu-id="c054f-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="c054f-154">Azure aboneliğiyle ilişkili uygulama hizmetlerinin listesini görüntülemek için **Uygulama Hizmetleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="c054f-155">Bu öğreticinin önceki bölümünde oluşturulan web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="c054f-156">**Dağıtım** bıçağında, **Dağıtım seçeneklerini** > seçin Kaynak > **Yerel Git Deposu'nu\*\*\*\*seçin.**</span><span class="sxs-lookup"><span data-stu-id="c054f-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Ayarlar bıçak: Dağıtım kaynak bıçak: Kaynak bıçak seçin](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="c054f-158">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-158">Select **OK**.</span></span>

1. <span data-ttu-id="c054f-159">Bir web uygulamasını veya başka bir Uygulama Hizmeti uygulamasını yayımlamaiçin dağıtım kimlik bilgileri daha önce ayarlanmadıysa, bunları şimdi ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c054f-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="c054f-160">**Ayarlar** > **Dağıtım kimlik bilgilerini**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="c054f-161">**Dağıtım kimlik bilgilerini ayarla** görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c054f-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="c054f-162">Bir kullanıcı adı ve parola oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c054f-162">Create a user name and password.</span></span> <span data-ttu-id="c054f-163">Parolayı Git'i ayarlarken daha sonra kullanmak üzere kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c054f-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="c054f-164">**Kaydet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-164">Select **Save**.</span></span>

1. <span data-ttu-id="c054f-165">Web **App** blade'inde **Ayarlar** > **Özellikleri'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="c054f-166">Dağıtılabilmek için dağıtılabilmek için uzak Git deposunun **URL'si GIT**URL'si altında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="c054f-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="c054f-167">Öğreticide daha sonra kullanmak üzere **GIT URL** değerini kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="c054f-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: uygulama Özellikleri bıçak](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="c054f-169">Web uygulamasını Azure App Service’te yayımlama</span><span class="sxs-lookup"><span data-stu-id="c054f-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="c054f-170">Bu bölümde, Visual Studio'yu kullanarak yerel bir Git deposu oluşturun ve web uygulamasını dağıtmak için bu depodan Azure'a itin.</span><span class="sxs-lookup"><span data-stu-id="c054f-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="c054f-171">Söz konusu adımlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c054f-171">The steps involved include the following:</span></span>

* <span data-ttu-id="c054f-172">Yerel deponun Azure'a dağıtılaabilmesi için GIT URL değerini kullanarak uzak depo ayarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c054f-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="c054f-173">Proje değişiklikleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="c054f-173">Commit project changes.</span></span>
* <span data-ttu-id="c054f-174">Proje değişikliklerini yerel depodan Azure'daki uzak depoya taşıyın.</span><span class="sxs-lookup"><span data-stu-id="c054f-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="c054f-175">**Solution Explorer'da** **Çözüm 'SampleWebAppDemo'** seçeneğini sağ tıklatın ve **Commit'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="c054f-176">**Takım Gezgini** görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c054f-176">The **Team Explorer** is displayed.</span></span>

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="c054f-178">**Takım Gezgini'nde,** **Ayarlar** > Deposu Ayarları'> **Ana Sayfa** (ev simgesi) seçeneğini**belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="c054f-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="c054f-179">**Depo Ayarları**’nın **Uzak öğeler** bölümünde **Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="c054f-180">**Uzak Öğe Ekle** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c054f-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="c054f-181">Uzaktan kumandanın **adını** **Azure-SampleApp**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c054f-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="c054f-182">Bu öğreticide Azure'dan kopyalanan **Git URL'sine** **Getir** değerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c054f-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="c054f-183">Bu URL ile biten **.git**olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="c054f-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Uzak iletişim kutusunu edin](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="c054f-185">Alternatif olarak, **Komut Penceresi'ni**açarak, proje dizinine değiştirerek ve komutu girerek Komut **Penceresinden** uzak depoyu belirtin.</span><span class="sxs-lookup"><span data-stu-id="c054f-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="c054f-186">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c054f-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="c054f-187"> >  **Ayarla\r\*\*\**Genel Ayarları**> **Ana Sayfa** (ana sayfa simgesi) seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="c054f-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="c054f-188">Adın ve e-posta adresinin ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="c054f-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="c054f-189">Gerekirse **Güncelleştir'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="c054f-190">**Değişiklikler** görünümüne dönmek için **Ana Sayfa** > **Değişiklikleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="c054f-191">**İlk Itme #1** gibi bir ileti girin ve **Commit'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="c054f-192">Bu eylem yerel bir *commit* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c054f-192">This action creates a *commit* locally.</span></span>

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="c054f-194">Alternatif olarak, **Komut Penceresi'ni**açarak, proje dizinine değiştirerek ve git komutlarını girerek Komut **Penceresinden** değişiklikler gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="c054f-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="c054f-195">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c054f-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="c054f-196">**Ana Sayfa** > **Eşitleme** > **Eylemleri** > **Açık Komut İstem'ini**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="c054f-197">Komut istemi proje dizinine açılır.</span><span class="sxs-lookup"><span data-stu-id="c054f-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="c054f-198">Komut penceresine aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="c054f-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="c054f-199">Azure'da daha önce oluşturulan Azure **dağıtım kimlik bilgileri** parolasını girin.</span><span class="sxs-lookup"><span data-stu-id="c054f-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="c054f-200">Bu komut, yerel proje dosyalarını Azure'a itme işlemini başlatır.</span><span class="sxs-lookup"><span data-stu-id="c054f-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="c054f-201">Yukarıdaki komuttan gelen çıktı, dağıtımın başarılı olduğu iletisiyle sona erer.</span><span class="sxs-lookup"><span data-stu-id="c054f-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="c054f-202">Projede işbirliği gerekiyorsa, Azure'a geçmeden önce [GitHub'a](https://github.com) itmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="c054f-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="c054f-203">Etkin Dağıtımı Doğrula</span><span class="sxs-lookup"><span data-stu-id="c054f-203">Verify the Active Deployment</span></span>

<span data-ttu-id="c054f-204">Web uygulamasının yerel ortamdan Azure'a aktarılmasının başarılı olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c054f-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="c054f-205">Azure [Portalı'nda](https://portal.azure.com)web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="c054f-206">**Dağıtım** > **Dağıtım seçeneklerini**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portalı: Ayarlar bıçak: Başarılı dağıtım gösteren dağıtım lar](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="c054f-208">Azure’da uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c054f-208">Run the app in Azure</span></span>

<span data-ttu-id="c054f-209">Artık web uygulaması Azure'a dağıtılanın, uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c054f-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="c054f-210">Bu iki şekilde gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="c054f-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="c054f-211">Azure Portalı'nda, web uygulaması için web uygulama bıçaklarını bulun.</span><span class="sxs-lookup"><span data-stu-id="c054f-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="c054f-212">Uygulamayı varsayılan tarayıcıda görüntülemek için **Gözat'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="c054f-213">Bir tarayıcı açın ve web uygulamasının URL'sini girin.</span><span class="sxs-lookup"><span data-stu-id="c054f-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="c054f-214">Örnek: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="c054f-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="c054f-215">Web uygulamasını güncelleştirin ve yeniden yayımlayın</span><span class="sxs-lookup"><span data-stu-id="c054f-215">Update the web app and republish</span></span>

<span data-ttu-id="c054f-216">Yerel kodda değişiklik yaptıktan sonra yeniden yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="c054f-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="c054f-217">Visual Studio **Çözüm Explorer'da** *Startup.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="c054f-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="c054f-218">`Configure` Yöntemde, yöntemi `Response.WriteAsync` aşağıdaki gibi görünecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c054f-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="c054f-219">Değişiklikleri Startup.cs *kaydedin.*</span><span class="sxs-lookup"><span data-stu-id="c054f-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="c054f-220">**Solution Explorer'da**, **'SampleWebAppDemo' seçeneğini** sağ tıklatın ve **Commit'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="c054f-221">**Takım Gezgini** görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c054f-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="c054f-222">Bir commit iletisi `Update #2`girin, örneğin.</span><span class="sxs-lookup"><span data-stu-id="c054f-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="c054f-223">Proje değişikliklerini işlemek için **Commit** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="c054f-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="c054f-224">**Ev** > **Eşitleme** > **Eylemleri** > **Push'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="c054f-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="c054f-225">Alternatif olarak, **komut penceresini**açarak, proje dizinine değiştirerek ve git komutunu girerek değişiklikleri Komut **Penceresinden** itin.</span><span class="sxs-lookup"><span data-stu-id="c054f-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="c054f-226">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c054f-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="c054f-227">Azure'da güncelleştirilmiş web uygulamasını görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="c054f-227">View the updated web app in Azure</span></span>

<span data-ttu-id="c054f-228">Azure Portalı'ndaki web uygulama bıçağından **Gözat'ı** seçerek veya bir tarayıcı açıp web uygulamasının URL'sini girerek güncelleştirilmiş web uygulamasını görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="c054f-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="c054f-229">Örnek: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="c054f-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c054f-230">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c054f-230">Additional resources</span></span>

* [<span data-ttu-id="c054f-231">Azure Ardışık Hatları ile ilk ardışık sisteminizi oluşturun</span><span class="sxs-lookup"><span data-stu-id="c054f-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="c054f-232">Kudu Projesi</span><span class="sxs-lookup"><span data-stu-id="c054f-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
