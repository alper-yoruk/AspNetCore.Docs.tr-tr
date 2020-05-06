---
title: ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım
author: rick-anderson
description: Visual Studio kullanarak ASP.NET Core bir Web uygulaması oluşturmayı ve sürekli dağıtım için git 'i kullanarak Azure App Service nasıl dağıtacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3a865c2c42cb71e109331675460456a27dc500fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775316"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="239fd-103">ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım</span><span class="sxs-lookup"><span data-stu-id="239fd-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="239fd-104">By [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="239fd-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="239fd-105">Bu öğreticide, Visual Studio kullanarak ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı ve Visual Studio 'dan sürekli dağıtım kullanarak Azure App Service nasıl dağıtılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="239fd-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="239fd-106">Ayrıca, Azure DevOps Services kullanarak [Azure App Service](/azure/app-service/app-service-web-overview) için sürekli teslım (CD) iş akışını yapılandırmayı gösteren [Azure Pipelines ilk Işlem hattınızı oluşturun](/azure/devops/pipelines/get-started-yaml).</span><span class="sxs-lookup"><span data-stu-id="239fd-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="239fd-107">Azure Pipelines (bir Azure DevOps Services hizmeti), Azure App Service 'de barındırılan uygulamalar için güncelleştirmeleri yayımlamak üzere güçlü bir dağıtım işlem hattı ayarlamayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="239fd-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="239fd-108">İşlem hattı, Azure portal derlemek, testler çalıştırmak, bir hazırlama yuvasına dağıtmak ve sonra üretime dağıtmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="239fd-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="239fd-109">Bu öğreticiyi tamamlayabilmeniz için bir Microsoft Azure hesabı gereklidir.</span><span class="sxs-lookup"><span data-stu-id="239fd-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="239fd-110">Bir hesap almak için [MSDN abone avantajlarını etkinleştirin](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) veya [ücretsiz deneme için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="239fd-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="239fd-111">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="239fd-111">Prerequisites</span></span>

<span data-ttu-id="239fd-112">Bu öğreticide aşağıdaki yazılımların yüklü olduğu varsayılmaktadır:</span><span class="sxs-lookup"><span data-stu-id="239fd-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="239fd-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="239fd-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="239fd-114">Windows için [Git](https://git-scm.com/downloads)</span><span class="sxs-lookup"><span data-stu-id="239fd-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="239fd-115">ASP.NET Core web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="239fd-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="239fd-116">Visual Studio’yu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="239fd-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="239fd-117">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="239fd-118">**ASP.NET Core Web uygulaması** proje şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="239fd-119">**Yüklü** > **Templates**şablonlar > **Visual**C# > **.NET Core**altında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="239fd-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="239fd-120">Projeyi `SampleWebAppDemo`adlandırın.</span><span class="sxs-lookup"><span data-stu-id="239fd-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="239fd-121">**Yeni git deposu oluştur** seçeneğini belirleyip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Yeni Proje iletişim kutusu](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="239fd-123">**Yeni ASP.NET Core projesi** Iletişim kutusunda **boş** şablon ASP.NET Core seçin ve ardından **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Yeni ASP.NET Core projesi iletişim kutusu](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="239fd-125">.NET Core 'un en son sürümü 2,0 ' dir.</span><span class="sxs-lookup"><span data-stu-id="239fd-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="239fd-126">Web uygulamasını yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="239fd-126">Running the web app locally</span></span>

1. <span data-ttu-id="239fd-127">Visual Studio uygulamayı oluşturmayı tamamladıktan sonra **Hata Ayıkla** > **başlatma hata ayıklamayı**seçerek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="239fd-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="239fd-128">Alternatif olarak **F5**tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="239fd-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="239fd-129">Visual Studio 'Yu ve yeni uygulamayı başlatmak zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="239fd-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="239fd-130">Tamamlandıktan sonra tarayıcı, çalışan uygulamayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="239fd-130">Once it's complete, the browser shows the running app.</span></span>

   ![' Merhaba Dünya! ' görüntüleyen uygulamanın çalıştığını gösteren tarayıcı penceresi](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="239fd-132">Çalışan Web uygulamasını inceledikten sonra, tarayıcıyı kapatın ve Visual Studio araç çubuğunda "hata ayıklamayı Durdur" simgesini seçerek uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="239fd-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="239fd-133">Azure portalında bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="239fd-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="239fd-134">Aşağıdaki adımlar Azure portalında bir Web uygulaması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="239fd-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="239fd-135">[Azure portalında](https://portal.azure.com)oturum açın.</span><span class="sxs-lookup"><span data-stu-id="239fd-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="239fd-136">Portal arabiriminin sol üst kısmındaki **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="239fd-137">**Web ve mobil** > **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portal: yeni düğme: market altında Web ve Mobil: öne çıkan uygulamalar altında Web uygulaması düğmesi](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="239fd-139">**Web uygulaması** dikey penceresinde, **App Service adı**için benzersiz bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="239fd-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Web uygulaması dikey penceresi](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="239fd-141">**App Service ad** adı benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="239fd-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="239fd-142">Ad sağlandığında Portal bu kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="239fd-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="239fd-143">Farklı bir değer sağlıyorsanız, bu öğreticide her **Samplewebappdemo** oluşumu için bu değeri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="239fd-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="239fd-144">Ayrıca, **Web uygulaması** dikey penceresinde, mevcut bir **App Service planı/konumu** seçin veya yeni bir tane oluşturun.</span><span class="sxs-lookup"><span data-stu-id="239fd-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="239fd-145">Yeni bir plan oluşturuyorsanız, fiyatlandırma katmanını, konumunu ve diğer seçenekleri seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="239fd-146">App Service planları hakkında daha fazla bilgi için bkz. [Azure App Service planlar ayrıntılı genel bakış](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="239fd-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="239fd-147">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-147">Select **Create**.</span></span> <span data-ttu-id="239fd-148">Azure, Web uygulamasını sağlayacak ve başlatacak.</span><span class="sxs-lookup"><span data-stu-id="239fd-148">Azure will provision and start the web app.</span></span>

   ![Azure portalı: örnek Web uygulaması tanıtımı 01 Essentials dikey penceresi](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="239fd-150">Yeni Web uygulaması için git yayımlamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="239fd-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="239fd-151">Git, bir Azure App Service Web uygulaması dağıtmak için kullanılabilen bir dağıtılmış sürüm denetim sistemidir.</span><span class="sxs-lookup"><span data-stu-id="239fd-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="239fd-152">Web uygulaması kodu yerel bir git deposunda depolanır ve kod, uzak bir depoya ileterek Azure 'a dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="239fd-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="239fd-153">[Azure portalında](https://portal.azure.com)oturum açın.</span><span class="sxs-lookup"><span data-stu-id="239fd-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="239fd-154">Azure aboneliğiyle ilişkili uygulama hizmetlerinin listesini görüntülemek için **uygulama hizmetleri** ' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="239fd-155">Bu öğreticinin önceki bölümünde oluşturulan Web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="239fd-156">**Dağıtım** dikey penceresinde **dağıtım seçenekleri** > **kaynak** > **yerel Git deposu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Ayarlar dikey penceresi: dağıtım kaynağı dikey penceresi: kaynak dikey penceresini seçin](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="239fd-158">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-158">Select **OK**.</span></span>

1. <span data-ttu-id="239fd-159">Bir Web uygulamasını yayınlamak için dağıtım kimlik bilgileri App Service veya daha önce ayarlanmamışsa, bu uygulamaları şimdi ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="239fd-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="239fd-160">**Ayarlar** > **dağıtım kimlik bilgileri**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="239fd-161">**Dağıtım kimlik bilgilerini ayarla** dikey penceresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="239fd-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="239fd-162">Bir kullanıcı adı ve parola oluşturun.</span><span class="sxs-lookup"><span data-stu-id="239fd-162">Create a user name and password.</span></span> <span data-ttu-id="239fd-163">Git ayarlanırken daha sonra kullanmak üzere parolayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="239fd-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="239fd-164">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-164">Select **Save**.</span></span>

1. <span data-ttu-id="239fd-165">**Web uygulaması** dikey penceresinde **Ayarlar** > **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="239fd-166">Dağıtım yapılacak uzak git deposunun URL 'SI **GIT URL 'si**altında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="239fd-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="239fd-167">Öğreticide daha sonra kullanmak için **GIT URL 'si** değerini kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure portalı: uygulama özellikleri dikey penceresi](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="239fd-169">Web uygulamasını Azure App Service’te yayımlama</span><span class="sxs-lookup"><span data-stu-id="239fd-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="239fd-170">Bu bölümde, Visual Studio 'Yu kullanarak yerel bir git deposu oluşturun ve Web uygulamasını dağıtmak için bu depodan Azure 'a gönderin.</span><span class="sxs-lookup"><span data-stu-id="239fd-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="239fd-171">Söz konusu adımlar şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="239fd-171">The steps involved include the following:</span></span>

* <span data-ttu-id="239fd-172">Yerel depo Azure 'a dağıtılabilmesi için GIT URL 'SI değerini kullanarak uzak depo ayarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="239fd-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="239fd-173">Proje değişikliklerini Yürüt.</span><span class="sxs-lookup"><span data-stu-id="239fd-173">Commit project changes.</span></span>
* <span data-ttu-id="239fd-174">Yerel depodan Azure 'daki uzak depoya proje değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="239fd-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="239fd-175">**Çözüm Gezgini** **' Samplewebappdemo ' çözümüne** sağ tıklayın ve **Yürüt**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="239fd-176">**Takım Gezgini** görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="239fd-176">The **Team Explorer** is displayed.</span></span>

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="239fd-178">**Takım Gezgini** **, >** **ayarları** > **Depo ayarları**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="239fd-179">**Depo Ayarları**’nın **Uzak öğeler** bölümünde **Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="239fd-180">**Uzak Öğe Ekle** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="239fd-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="239fd-181">Uzak **adını** **Azure-SampleApp**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="239fd-182">**Fetch** için değeri bu öğreticide daha önce Azure 'Dan KOPYALANMıŞ **Git URL** 'sine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="239fd-183">Bunun, **. git**Ile biten URL olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Uzak iletişim kutusunu Düzenle](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="239fd-185">Alternatif olarak, **komut penceresinden komut** **penceresini açıp, proje**dizinine giderek ve komutu girerek, uzak depoyu belirtin.</span><span class="sxs-lookup"><span data-stu-id="239fd-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="239fd-186">Örnek:</span><span class="sxs-lookup"><span data-stu-id="239fd-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="239fd-187">**Settings**Ayarlar > **genel ayarları**> **giriş** (giriş simgesi) seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="239fd-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="239fd-188">Adın ve e-posta adresinin ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="239fd-189">Gerekirse **Güncelleştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="239fd-190">**Değişiklikler** görünümüne geri dönmek için **ana** > **değişiklikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="239fd-191">**Ilk gönderme #1** gibi bir teslim iletisi girin ve **Kaydet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="239fd-192">Bu eylem yerel olarak bir *işleme* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="239fd-192">This action creates a *commit* locally.</span></span>

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="239fd-194">Alternatif olarak **, komut penceresini açıp, proje**dizini olarak değiştirerek ve git komutlarını girerek **komut penceresinden** değişiklikleri işleyin.</span><span class="sxs-lookup"><span data-stu-id="239fd-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="239fd-195">Örnek:</span><span class="sxs-lookup"><span data-stu-id="239fd-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="239fd-196">**Ana** > **eşitleme** > **Actions**eylemlerini seçin**komut istemi 'ni açın.** > </span><span class="sxs-lookup"><span data-stu-id="239fd-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="239fd-197">Komut istemi proje dizini için açılır.</span><span class="sxs-lookup"><span data-stu-id="239fd-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="239fd-198">Komut penceresine aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="239fd-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="239fd-199">Azure 'da daha önce oluşturulan Azure **dağıtım kimlik bilgileri** parolasını girin.</span><span class="sxs-lookup"><span data-stu-id="239fd-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="239fd-200">Bu komut, yerel proje dosyalarını Azure 'a iletme işlemini başlatır.</span><span class="sxs-lookup"><span data-stu-id="239fd-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="239fd-201">Yukarıdaki komutun çıktısı, dağıtımın başarılı olduğunu belirten bir iletiyle biter.</span><span class="sxs-lookup"><span data-stu-id="239fd-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="239fd-202">Projede işbirliği gerekiyorsa, Azure 'a göndermeden önce [GitHub](https://github.com) 'a göndermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="239fd-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="239fd-203">Etkin dağıtımı doğrulama</span><span class="sxs-lookup"><span data-stu-id="239fd-203">Verify the Active Deployment</span></span>

<span data-ttu-id="239fd-204">Yerel ortamdan Azure 'a Web uygulaması aktarımının başarılı olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="239fd-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="239fd-205">[Azure portalında](https://portal.azure.com)Web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="239fd-206">**Dağıtım** > **dağıtım seçeneklerini**belirleyin.</span><span class="sxs-lookup"><span data-stu-id="239fd-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure portalı: ayarlar dikey penceresi: başarılı dağıtımı gösteren dağıtımlar dikey penceresi](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="239fd-208">Azure’da uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="239fd-208">Run the app in Azure</span></span>

<span data-ttu-id="239fd-209">Web uygulaması Azure 'a dağıtıldığına göre, uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="239fd-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="239fd-210">Bu, iki şekilde gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="239fd-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="239fd-211">Azure portalında, Web uygulaması için Web uygulaması dikey penceresini bulun.</span><span class="sxs-lookup"><span data-stu-id="239fd-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="239fd-212">Uygulamayı varsayılan tarayıcıda görüntülemek için **Gözatma** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="239fd-213">Bir tarayıcı açın ve Web uygulamasının URL 'sini girin.</span><span class="sxs-lookup"><span data-stu-id="239fd-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="239fd-214">Örnek: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="239fd-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="239fd-215">Web uygulamasını güncelleştirme ve yeniden yayımlama</span><span class="sxs-lookup"><span data-stu-id="239fd-215">Update the web app and republish</span></span>

<span data-ttu-id="239fd-216">Yerel kodda değişiklikler yaptıktan sonra, yeniden yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="239fd-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="239fd-217">Visual Studio 'nun **Çözüm Gezgini** , *Startup.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="239fd-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="239fd-218">`Configure` Yönteminde, `Response.WriteAsync` yöntemi aşağıdaki şekilde görünecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="239fd-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="239fd-219">Değişiklikleri *Startup.cs*'ye kaydedin.</span><span class="sxs-lookup"><span data-stu-id="239fd-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="239fd-220">**Çözüm Gezgini** **' Samplewebappdemo ' çözümüne** sağ tıklayın ve **Yürüt**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="239fd-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="239fd-221">**Takım Gezgini** görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="239fd-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="239fd-222">Gibi bir kayıt iletisi girin `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="239fd-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="239fd-223">Proje değişikliklerini yürütmek için **Yürüt** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="239fd-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="239fd-224">**Ana** > **eşitleme** > **Actions**eylemleri Gönder ' i seçin.**Push** > </span><span class="sxs-lookup"><span data-stu-id="239fd-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="239fd-225">Alternatif olarak **, komut penceresini açıp, proje**dizinine değiştirerek ve bir git komutu girerek değişiklikleri **komut penceresinden** gönderin.</span><span class="sxs-lookup"><span data-stu-id="239fd-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="239fd-226">Örnek:</span><span class="sxs-lookup"><span data-stu-id="239fd-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="239fd-227">Azure 'da güncelleştirilmiş Web uygulamasını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="239fd-227">View the updated web app in Azure</span></span>

<span data-ttu-id="239fd-228">Azure portalındaki Web uygulaması dikey penceresinde veya **Browse** bir tarayıcı açıp Web uygulamasının URL 'sini girerek güncelleştirilmiş Web uygulamasını görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="239fd-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="239fd-229">Örnek: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="239fd-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="239fd-230">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="239fd-230">Additional resources</span></span>

* [<span data-ttu-id="239fd-231">Azure Pipelines ile ilk işlem hattınızı oluşturma</span><span class="sxs-lookup"><span data-stu-id="239fd-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="239fd-232">Kudu Projesi</span><span class="sxs-lookup"><span data-stu-id="239fd-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
