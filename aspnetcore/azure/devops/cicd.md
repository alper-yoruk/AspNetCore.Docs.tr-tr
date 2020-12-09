---
title: ASP.NET Core ve Azure ile sürekli tümleştirme ve dağıtım-DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps 'da sürekli tümleştirme ve dağıtım
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901216"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="625a3-103">Sürekli tümleştirme ve dağıtım</span><span class="sxs-lookup"><span data-stu-id="625a3-103">Continuous integration and deployment</span></span>

<span data-ttu-id="625a3-104">Önceki bölümde, basit akış okuyucusu uygulaması için yerel bir git deposu oluşturdunuz.</span><span class="sxs-lookup"><span data-stu-id="625a3-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="625a3-105">Bu bölümde, bu kodu bir GitHub deposuna yayımlayacaksınız ve Azure Pipelines kullanarak Azure DevOps Services bir işlem hattı oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="625a3-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="625a3-106">İşlem hattı, uygulamanın sürekli derlemelerini ve dağıtımlarını mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="625a3-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="625a3-107">GitHub deposuna yapılan her türlü kayıt, bir derlemeyi ve Azure Web uygulamasının hazırlama yuvasına bir dağıtımını tetikler.</span><span class="sxs-lookup"><span data-stu-id="625a3-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="625a3-108">Bu bölümde, aşağıdaki görevleri tamamlayacaksınız:</span><span class="sxs-lookup"><span data-stu-id="625a3-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="625a3-109">Uygulamanın kodunu GitHub 'a yayımlama</span><span class="sxs-lookup"><span data-stu-id="625a3-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="625a3-110">Yerel git dağıtımının bağlantısını kes</span><span class="sxs-lookup"><span data-stu-id="625a3-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="625a3-111">Azure DevOps kuruluşu oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="625a3-112">Azure DevOps Services takım projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="625a3-113">Derleme tanımı oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-113">Create a build definition</span></span>
* <span data-ttu-id="625a3-114">Yayın işlem hattı oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-114">Create a release pipeline</span></span>
* <span data-ttu-id="625a3-115">GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma</span><span class="sxs-lookup"><span data-stu-id="625a3-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="625a3-116">Azure Pipelines işlem hattını inceleyin</span><span class="sxs-lookup"><span data-stu-id="625a3-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="625a3-117">Uygulamanın kodunu GitHub 'a yayımlama</span><span class="sxs-lookup"><span data-stu-id="625a3-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="625a3-118">Bir tarayıcı penceresi açın ve adresine gidin `https://github.com` .</span><span class="sxs-lookup"><span data-stu-id="625a3-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="625a3-119">**+** Başlıktaki aşağı açılan düğmesine tıklayın ve **yeni depo**' ı seçin:</span><span class="sxs-lookup"><span data-stu-id="625a3-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub yeni depo seçeneği](media/cicd/github-new-repo.png)

1. <span data-ttu-id="625a3-121">**Sahip** açılır penceresinde hesabınızı seçin ve **Depo adı** metin kutusuna *basit-Feed-Reader* girin.</span><span class="sxs-lookup"><span data-stu-id="625a3-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="625a3-122">**Depo oluştur** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="625a3-123">Yerel makinenizin komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="625a3-123">Open your local machine's command shell.</span></span> <span data-ttu-id="625a3-124">*Basit akış okuyucusu* git deposunun depolandığı dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="625a3-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="625a3-125">Var olan *kaynağı* uzak *yukarı akış* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="625a3-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="625a3-126">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="625a3-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="625a3-127">GitHub 'daki deponun kopyasına işaret eden yeni bir *Başlangıç noktası* ekleyin.</span><span class="sxs-lookup"><span data-stu-id="625a3-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="625a3-128">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="625a3-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="625a3-129">Yerel git deponuzu yeni oluşturulan GitHub deposuna yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="625a3-130">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="625a3-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="625a3-131">Bir tarayıcı penceresi açın ve adresine gidin `https://github.com/<GitHub_username>/simple-feed-reader/` .</span><span class="sxs-lookup"><span data-stu-id="625a3-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="625a3-132">Kodunuzun GitHub deposunda göründüğünü doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="625a3-133">Yerel git dağıtımının bağlantısını kes</span><span class="sxs-lookup"><span data-stu-id="625a3-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="625a3-134">Yerel git dağıtımını aşağıdaki adımlarla kaldırın.</span><span class="sxs-lookup"><span data-stu-id="625a3-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="625a3-135">Azure Pipelines (bir Azure DevOps hizmeti) her ikisi de bu işlevi değiştirir ve azaltır.</span><span class="sxs-lookup"><span data-stu-id="625a3-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="625a3-136">[Azure Portal](https://portal.azure.com/)açın ve *hazırlama (MyWebApp \<unique_number\> /hazırlama)* Web uygulamasına gidin.</span><span class="sxs-lookup"><span data-stu-id="625a3-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="625a3-137">Web uygulaması, portalın arama kutusuna *hazırlama* girilerek hızlı bir şekilde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="625a3-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![hazırlama Web uygulaması arama terimi](media/cicd/portal-search-box.png)

1. <span data-ttu-id="625a3-139">**Dağıtım Merkezi**' ne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-139">Click **Deployment Center**.</span></span> <span data-ttu-id="625a3-140">Yeni bir panel belirir.</span><span class="sxs-lookup"><span data-stu-id="625a3-140">A new panel appears.</span></span> <span data-ttu-id="625a3-141">Önceki bölümde eklenen yerel git kaynak denetimi yapılandırmasını kaldırmak için **bağlantıyı kes** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="625a3-142">**Evet** düğmesine tıklayarak kaldırma işlemini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="625a3-143">*MyWebApp<unique_number>* App Service gidin.</span><span class="sxs-lookup"><span data-stu-id="625a3-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="625a3-144">Bir anımsatıcı olarak, App Service hızlı bir şekilde bulmak için portalın arama kutusu kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="625a3-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="625a3-145">**Dağıtım Merkezi**' ne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-145">Click **Deployment Center**.</span></span> <span data-ttu-id="625a3-146">Yeni bir panel belirir.</span><span class="sxs-lookup"><span data-stu-id="625a3-146">A new panel appears.</span></span> <span data-ttu-id="625a3-147">Önceki bölümde eklenen yerel git kaynak denetimi yapılandırmasını kaldırmak için **bağlantıyı kes** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="625a3-148">**Evet** düğmesine tıklayarak kaldırma işlemini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="625a3-149">Azure DevOps kuruluşu oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="625a3-150">Bir tarayıcı açın ve [Azure DevOps kuruluş oluşturma sayfasına](https://go.microsoft.com/fwlink/?LinkId=307137)gidin.</span><span class="sxs-lookup"><span data-stu-id="625a3-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="625a3-151">Azure DevOps kuruluşunuza erişmek için URL 'YI biçimlendirmek üzere **hatırlayabileceğiniz bir ad seçin** metin kutusuna benzersiz bir ad yazın.</span><span class="sxs-lookup"><span data-stu-id="625a3-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="625a3-152">Kod bir GitHub deposunda barındırıldığından **Git** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="625a3-153">**Devam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-153">Click the **Continue** button.</span></span> <span data-ttu-id="625a3-154">Kısa bir bekleme sonrasında, *Myfirstproject* adlı bir hesap ve takım projesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="625a3-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps kuruluş oluşturma sayfası](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="625a3-156">Azure DevOps kuruluşunun ve projesinin kullanıma hazırlandığını belirten onay e-postasını açın.</span><span class="sxs-lookup"><span data-stu-id="625a3-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="625a3-157">**Projenize başla** düğmesine tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-157">Click the **Start your project** button:</span></span>

    ![Proje düğmesini başlatın](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="625a3-159">Bir tarayıcı, *\<account_name\> . VisualStudio.com* için açılır.</span><span class="sxs-lookup"><span data-stu-id="625a3-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="625a3-160">Projenin DevOps ardışık düzenini yapılandırmaya başlamak için *Myfirstproject* bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="625a3-161">Azure Pipelines işlem hattını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="625a3-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="625a3-162">Tamamlanacak üç farklı adım vardır.</span><span class="sxs-lookup"><span data-stu-id="625a3-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="625a3-163">Aşağıdaki üç bölümde bulunan adımların tamamlanması işlemsel bir DevOps işlem hattı ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="625a3-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="625a3-164">GitHub deposuna Azure DevOps erişimi verme</span><span class="sxs-lookup"><span data-stu-id="625a3-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="625a3-165">**Bir dış depodan gelen veya derleme kodunu** genişletin Accordion.</span><span class="sxs-lookup"><span data-stu-id="625a3-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="625a3-166">**Kurulum oluştur** düğmesine tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-166">Click the **Setup Build** button:</span></span>

    ![Kurulum oluştur düğmesi](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="625a3-168">**Kaynak seçin** bölümünde **GitHub** seçeneğini belirleyin:</span><span class="sxs-lookup"><span data-stu-id="625a3-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Kaynak seçin-GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="625a3-170">Azure DevOps 'ın GitHub deponuza erişebilmesi için yetkilendirme gereklidir.</span><span class="sxs-lookup"><span data-stu-id="625a3-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="625a3-171">**Bağlantı adı** metin kutusuna *GitHub bağlantısı><GitHub_username* girin.</span><span class="sxs-lookup"><span data-stu-id="625a3-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="625a3-172">Örnek:</span><span class="sxs-lookup"><span data-stu-id="625a3-172">For example:</span></span>

    ![GitHub bağlantı adı](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="625a3-174">GitHub hesabınızda iki öğeli kimlik doğrulaması etkinleştirilirse, kişisel erişim belirteci gereklidir.</span><span class="sxs-lookup"><span data-stu-id="625a3-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="625a3-175">Bu durumda, **GitHub kişisel erişim belirteci Ile yetkilendirme** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="625a3-176">Yardım için [resmi GitHub kişisel erişim belirteci oluşturma yönergelerine](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) bakın.</span><span class="sxs-lookup"><span data-stu-id="625a3-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="625a3-177">İzinlerin yalnızca *Depo* kapsamı gereklidir.</span><span class="sxs-lookup"><span data-stu-id="625a3-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="625a3-178">Aksi takdirde, **OAuth kullanarak Yetkilendir** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="625a3-179">İstendiğinde, GitHub hesabınızda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="625a3-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="625a3-180">Ardından, Azure DevOps kuruluşunuza erişim vermek için yetkilendir ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="625a3-181">Başarılı olursa, yeni bir hizmet uç noktası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="625a3-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="625a3-182">**Depo** düğmesinin yanındaki üç nokta düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="625a3-183">Listeden *<GitHub_username>/Simple-Feed-Reader* deposunu seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="625a3-184">**Seç** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="625a3-185">**El ile ve zamanlanmış yapılar açılır Için varsayılan daldan** varsayılan dalı (*ana*) seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-185">Select the default branch (*master*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="625a3-186">**Devam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-186">Click the **Continue** button.</span></span> <span data-ttu-id="625a3-187">Şablon seçimi sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="625a3-188">Derleme tanımı oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-188">Create the build definition</span></span>

1. <span data-ttu-id="625a3-189">Şablon Seçimi sayfasında, arama kutusuna *ASP.NET Core* girin:</span><span class="sxs-lookup"><span data-stu-id="625a3-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![Şablon sayfasında arama ASP.NET Core](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="625a3-191">Şablon arama sonuçları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-191">The template search results appear.</span></span> <span data-ttu-id="625a3-192">**ASP.NET Core** şablonun üzerine gelin ve **Uygula** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="625a3-193">Yapı tanımının **Görevler** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="625a3-194">**Tetikleyiciler** sekmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="625a3-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="625a3-195">**Sürekli tümleştirmeyi etkinleştir** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="625a3-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="625a3-196">**Dal filtreleri** bölümünde, **tür** açılır seçeneğinin *dahil* olarak ayarlandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="625a3-197">**Dal belirtimi** açılır öğesini *ana* olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Sürekli tümleştirme ayarlarını etkinleştir](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="625a3-199">Bu ayarlar, GitHub deposunun varsayılan dalına (*ana*) herhangi bir değişiklik gönderildiğinde bir yapılandırmanın tetiklenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="625a3-199">These settings cause a build to trigger when any change is pushed to the default branch (*master*) of the GitHub repository.</span></span> <span data-ttu-id="625a3-200">Sürekli tümleştirme, [GitHub 'daki değişiklikleri Yürüt ve Azure 'a otomatik olarak dağıt](#commit-changes-to-github-and-automatically-deploy-to-azure) bölümünde test edilir.</span><span class="sxs-lookup"><span data-stu-id="625a3-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="625a3-201">**& kuyruğu kaydet** düğmesine tıklayın ve **Kaydet** seçeneğini belirleyin:</span><span class="sxs-lookup"><span data-stu-id="625a3-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Kaydet düğmesi](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="625a3-203">Aşağıdaki kalıcı iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="625a3-203">The following modal dialog appears:</span></span>

    ![Derleme tanımını kaydet-kalıcı iletişim kutusu](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="625a3-205">Varsayılan klasörünü kullanın *\\* ve **Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="625a3-206">Yayın işlem hattını oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-206">Create the release pipeline</span></span>

1. <span data-ttu-id="625a3-207">Takım projenizin **yayınlar** sekmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="625a3-208">Yeni işlem **hattı** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-208">Click the **New pipeline** button.</span></span>

    ![Yayınlar sekmesi-yeni tanım düğmesi](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="625a3-210">Şablon Seçimi bölmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="625a3-211">Şablon Seçimi sayfasında, arama kutusuna *App Service* girin:</span><span class="sxs-lookup"><span data-stu-id="625a3-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Yayın işlem hattı şablonu arama kutusu](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="625a3-213">Şablon arama sonuçları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-213">The template search results appear.</span></span> <span data-ttu-id="625a3-214">**Yuva şablonuyla Azure App Service dağıtımının** üzerine gelin ve **Uygula** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="625a3-215">Yayın işlem hattının **ardışık düzen** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Yayın ardışık düzen işlem hattı sekmesi](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="625a3-217">**Yapıtlar** kutusunda **Ekle** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="625a3-218">**Yapıt Ekle** paneli görünür:</span><span class="sxs-lookup"><span data-stu-id="625a3-218">The **Add artifact** panel appears:</span></span>

    ![Yayın işlem hattı-yapıt bölmesi ekleme](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="625a3-220">**Kaynak türü** bölümünden **Yapı** kutucuğunu seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="625a3-221">Bu tür, yayın işlem hattının derleme tanımına bağlanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="625a3-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="625a3-222">**Proje** açılır listesinden *myfirstproject* ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="625a3-223">**Kaynak (derleme tanımı)** açılır listesinden derleme tanımı adı, *MYFIRSTPROJECT-ASP.NET Core-CI*' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="625a3-224">**Varsayılan sürüm** açılır listesinden *en son* ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="625a3-225">Bu seçenek derleme tanımının en son çalıştırması tarafından oluşturulan yapıtları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="625a3-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="625a3-226">**Kaynak diğer ad** metin kutusundaki metni *Drop* ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="625a3-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="625a3-227">**Ekle** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-227">Click the **Add** button.</span></span> <span data-ttu-id="625a3-228">**Yapıtlar** bölümü, değişiklikleri görüntüleyecek şekilde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="625a3-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="625a3-229">Sürekli dağıtımları etkinleştirmek için şimşek simgesine tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Yayın işlem hattı yapıtları-şimşek sürgüsü simgesi](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="625a3-231">Bu seçenek etkinken, her yeni derleme kullanılabilir olduğunda bir dağıtım oluşur.</span><span class="sxs-lookup"><span data-stu-id="625a3-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="625a3-232">Doğru bir **sürekli dağıtım tetikleme** paneli görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="625a3-233">Özelliği etkinleştirmek için iki durumlu düğmeye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="625a3-234">**Çekme isteği tetikleyicisini** etkinleştirmek gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="625a3-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="625a3-235">**Yapı Dalı filtreleri** bölümünde **Ekle** açılan düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="625a3-236">**Derleme tanımının varsayılan dal** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="625a3-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="625a3-237">Bu filtre, yayının yalnızca GitHub deposunun varsayılan dalından (*ana*) bir derleme için tetiklenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="625a3-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*master*).</span></span>
1. <span data-ttu-id="625a3-238">**Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-238">Click the **Save** button.</span></span> <span data-ttu-id="625a3-239">Elde edilen **kaydetme** kalıcı Iletişim kutusunda **Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="625a3-240">**Ortam 1** kutusuna tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="625a3-241">Sağ tarafta bir **ortam** paneli görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="625a3-242">**Ortam adı** metin kutusundaki *ortam 1* metnini *Üretim* olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="625a3-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Yayın işlem hattı-ortam adı metin kutusu](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="625a3-244">**Üretim** kutusunda **1 aşama, 2 görev** bağlantısına tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Yayın işlem hattı-üretim ortamı link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="625a3-246">Ortamın **Görevler** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="625a3-247">**Yuvaya Azure App Service dağıt** görevine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="625a3-248">Ayarları, sağdaki bir panelde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="625a3-249">**Azure aboneliği** açılır listesinden App Service ilişkili Azure aboneliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="625a3-250">Seçtikten sonra **Yetkilendir** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="625a3-251">**Uygulama türü** açılan listesinden *Web uygulaması* ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="625a3-252">**App Service adı** açılır listesinden *mywebapp/<unique_number/>* seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="625a3-253">**Kaynak grubu** açılır listesinden *AzureTutorial* öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="625a3-254">**Yuva** açılır listesinden *hazırlama* ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="625a3-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="625a3-255">**Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="625a3-256">Varsayılan yayın işlem hattı adının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="625a3-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="625a3-257">Düzenlemek için kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="625a3-258">Ad olarak *MyFirstProject-ASP.NET Core-CD* kullanın.</span><span class="sxs-lookup"><span data-stu-id="625a3-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Yayın işlem hattı adı](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="625a3-260">**Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="625a3-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="625a3-261">GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma</span><span class="sxs-lookup"><span data-stu-id="625a3-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="625a3-262">Visual Studio 'da *Simplefeedreader. sln* ' i açın.</span><span class="sxs-lookup"><span data-stu-id="625a3-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="625a3-263">Çözüm Gezgini, *Pages\ındex.cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="625a3-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="625a3-264">`<h2>Simple Feed Reader - V3</h2>`Olarak değiştirin `<h2>Simple Feed Reader - V4</h2>` .</span><span class="sxs-lookup"><span data-stu-id="625a3-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="625a3-265">**Ctrl** + **Shift** + Uygulamayı derlemek için CTRL SHIFT **B** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="625a3-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="625a3-266">Dosyayı GitHub deposuna yürütün.</span><span class="sxs-lookup"><span data-stu-id="625a3-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="625a3-267">Visual Studio 'nun *Takım Gezgini* sekmesindeki **değişiklikler** sayfasını kullanın veya yerel makinenin komut kabuğunu kullanarak aşağıdakini yürütün:</span><span class="sxs-lookup"><span data-stu-id="625a3-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="625a3-268">Varsayılan daldaki (*ana*) değişikliği GitHub deponuzun *kaynak* uzak adına gönderin.</span><span class="sxs-lookup"><span data-stu-id="625a3-268">Push the change in the default branch (*master*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="625a3-269">Aşağıdaki komutta yer tutucusunu `{BRANCH}` varsayılan Dalla değiştirin (kullanın `master` ):</span><span class="sxs-lookup"><span data-stu-id="625a3-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `master`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="625a3-270">Kayıt, GitHub deposunun varsayılan dalında (*ana*) görünür:</span><span class="sxs-lookup"><span data-stu-id="625a3-270">The commit appears in the GitHub repository's default branch (*master*):</span></span>

    ![Varsayılan dalda GitHub yürütmesi (ana)](media/cicd/github-commit.png)

    <span data-ttu-id="625a3-272">Derleme, derleme tanımının **Tetikleyiciler** sekmesinde sürekli tümleştirme etkinleştirildiğinden tetiklenir:</span><span class="sxs-lookup"><span data-stu-id="625a3-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Sürekli tümleştirmeyi etkinleştir](media/cicd/enable-ci.png)

1. <span data-ttu-id="625a3-274">Azure DevOps Services **Azure Pipelines** yapılar sayfasının **sıraya alınmış** sekmesine gidin  >  **Builds** .</span><span class="sxs-lookup"><span data-stu-id="625a3-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="625a3-275">Sıraya alınan yapı, derlemeyi tetikleyen dalı ve yürütmeyi gösterir:</span><span class="sxs-lookup"><span data-stu-id="625a3-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![Kuyruğa Alınan derleme](media/cicd/build-queued.png)

1. <span data-ttu-id="625a3-277">Oluşturma işlemi başarılı olduktan sonra Azure 'a yönelik bir dağıtım oluşur.</span><span class="sxs-lookup"><span data-stu-id="625a3-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="625a3-278">Tarayıcıdaki uygulamaya gidin.</span><span class="sxs-lookup"><span data-stu-id="625a3-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="625a3-279">Başlıkta "v4" metninin göründüğünü unutmayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-279">Notice that the "V4" text appears in the heading:</span></span>

    ![güncelleştirilmiş uygulama](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="625a3-281">Azure Pipelines işlem hattını inceleyin</span><span class="sxs-lookup"><span data-stu-id="625a3-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="625a3-282">Derleme tanımı</span><span class="sxs-lookup"><span data-stu-id="625a3-282">Build definition</span></span>

<span data-ttu-id="625a3-283">*MyFirstProject-ASP.NET Core-CI* adında bir derleme tanımı oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="625a3-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="625a3-284">Tamamlandıktan sonra, derleme yayımlanacak varlıkları içeren bir *. zip* dosyası üretir.</span><span class="sxs-lookup"><span data-stu-id="625a3-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="625a3-285">Yayın işlem hattı bu varlıkları Azure 'a dağıtır.</span><span class="sxs-lookup"><span data-stu-id="625a3-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="625a3-286">Yapı tanımının **Görevler** sekmesi, kullanılan adımları listeler.</span><span class="sxs-lookup"><span data-stu-id="625a3-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="625a3-287">Beş derleme görevi vardır.</span><span class="sxs-lookup"><span data-stu-id="625a3-287">There are five build tasks.</span></span>

![derleme tanımı görevleri](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="625a3-289">**Geri yükle** &mdash; `dotnet restore` Uygulamanın NuGet paketlerini geri yüklemek için komutunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="625a3-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="625a3-290">Kullanılan varsayılan paket akışı nuget.org ' dir.</span><span class="sxs-lookup"><span data-stu-id="625a3-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="625a3-291">**Derleme** &mdash; `dotnet build --configuration release` Uygulamanın kodunu derlemek için komutunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="625a3-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="625a3-292">Bu `--configuration` seçenek, bir üretim ortamına dağıtım için uygun olan, kodun iyileştirilmiş bir sürümünü oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="625a3-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="625a3-293">Örneğin, bir hata ayıklama yapılandırması gerekiyorsa, derleme tanımının **değişkenler** sekmesinde *buildconfiguration* değişkenini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="625a3-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="625a3-294">**Test et** &mdash; `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` Uygulamanın birim testlerini çalıştırmak için komutunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="625a3-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="625a3-295">Birim testleri, glob düzeniyle eşleşen herhangi bir C# projesi içinde yürütülür `**/*Tests/*.csproj` .</span><span class="sxs-lookup"><span data-stu-id="625a3-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="625a3-296">Test sonuçları, seçeneği tarafından belirtilen konumdaki bir *. trx* dosyasına kaydedilir `--results-directory` .</span><span class="sxs-lookup"><span data-stu-id="625a3-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="625a3-297">Herhangi bir test başarısız olursa, yapı başarısız olur ve dağıtılmaz.</span><span class="sxs-lookup"><span data-stu-id="625a3-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="625a3-298">Birim testlerinin çalışmasını doğrulamak için, *Simplefeedreader. Tests\Services\NewsServiceTests.cs* ' yi, testlerin birini tam olarak kesin olarak bölmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="625a3-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="625a3-299">Örneğin, `Assert.True(result.Count > 0);` yönteminde olarak değiştirin `Assert.False(result.Count > 0);` `Returns_News_Stories_Given_Valid_Uri` .</span><span class="sxs-lookup"><span data-stu-id="625a3-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="625a3-300">Değişiklikleri yürütün ve GitHub 'a gönderin.</span><span class="sxs-lookup"><span data-stu-id="625a3-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="625a3-301">Derleme tetiklenir ve başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="625a3-301">The build is triggered and fails.</span></span> <span data-ttu-id="625a3-302">Derleme ardışık düzeni durumu **başarısız** olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="625a3-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="625a3-303">Değişikliği, yürütmeyi ve yeniden gönderin.</span><span class="sxs-lookup"><span data-stu-id="625a3-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="625a3-304">Derleme başarılı oldu.</span><span class="sxs-lookup"><span data-stu-id="625a3-304">The build succeeds.</span></span>

1. <span data-ttu-id="625a3-305">**Yayımla** &mdash; `dotnet publish --configuration release --output <local_path_on_build_agent>` Dağıtılacak yapıtlar içeren bir *. zip* dosyası üretmek için komutunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="625a3-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="625a3-306">`--output`Seçeneği, *. zip* dosyasının yayımlama konumunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="625a3-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="625a3-307">Bu konum adlı [önceden tanımlanmış bir değişken](/azure/devops/pipelines/build/variables) geçirilerek belirtilir `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="625a3-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="625a3-308">Bu değişken, derleme aracısında *c:\agent \_ work\1\a* gibi bir yerel yola genişletilir.</span><span class="sxs-lookup"><span data-stu-id="625a3-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="625a3-309">**Yapıtı Yayımla** &mdash; **Yayımla** görevi tarafından oluşturulan *. zip* dosyasını yayımlar.</span><span class="sxs-lookup"><span data-stu-id="625a3-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="625a3-310">Görev *. zip* dosya konumunu bir parametre olarak kabul eder, bu, önceden tanımlanmış değişkenidir `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="625a3-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="625a3-311">*. Zip* dosyası *Drop* adlı bir klasör olarak yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="625a3-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="625a3-312">Tanım içeren derlemelerin geçmişini görüntülemek için derleme tanımının **Özet** bağlantısına tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Derleme tanımı geçmişini gösteren ekran görüntüsü](media/cicd/build-definition-summary.png)

<span data-ttu-id="625a3-314">Sonuç sayfasında, benzersiz derleme numarasına karşılık gelen bağlantıya tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Derleme tanımı Özet sayfasını gösteren ekran görüntüsü](media/cicd/build-definition-completed.png)

<span data-ttu-id="625a3-316">Bu özel derleme özeti görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="625a3-317">**Yapılar** sekmesine tıklayın ve yapı tarafından üretilen *bırakma* klasörünün listelendiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Derleme tanımı yapıtları-bırakma klasörünü gösteren ekran görüntüsü](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="625a3-319">Yayımlanan yapıtları incelemek için **İndir** ve **keşfet** bağlantılarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="625a3-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="625a3-320">Yayın işlem hattı</span><span class="sxs-lookup"><span data-stu-id="625a3-320">Release pipeline</span></span>

<span data-ttu-id="625a3-321">*MyFirstProject-ASP.NET Core-CD* adlı bir yayın işlem hattı oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="625a3-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Yayın ardışık düzenine genel bakış gösteren ekran görüntüsü](media/cicd/release-definition-overview.png)

<span data-ttu-id="625a3-323">Yayın işlem hattının iki ana bileşeni **yapıtlar** ve **ortamlardır**.</span><span class="sxs-lookup"><span data-stu-id="625a3-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="625a3-324">**Yapıtlar** bölümündeki kutuya tıklanması aşağıdaki paneli ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="625a3-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Yayın işlem hattı yapılarını gösteren ekran görüntüsü](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="625a3-326">**Kaynak (derleme tanımı)** değeri, bu yayın işlem hattının bağlı olduğu derleme tanımını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="625a3-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="625a3-327">Yapı tanımının başarılı bir çalışması tarafından oluşturulan *. zip* dosyası, Azure 'a dağıtım için *Üretim* ortamına sağlanır.</span><span class="sxs-lookup"><span data-stu-id="625a3-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="625a3-328">Yayın ardışık düzen görevlerini görüntülemek için, *Üretim* ortamı kutusunda *1 aşama, 2 görev* bağlantısına tıklayın:</span><span class="sxs-lookup"><span data-stu-id="625a3-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Yayın ardışık düzen görevlerini gösteren ekran görüntüsü](media/cicd/release-definition-tasks.png)

<span data-ttu-id="625a3-330">Yayın işlem hattı iki görevden oluşur: *yuvaya Azure App Service dağıtın* ve *Azure App Service yuvası değiştirme 'yi yönetir*.</span><span class="sxs-lookup"><span data-stu-id="625a3-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="625a3-331">İlk göreve tıkladığınızda aşağıdaki görev yapılandırması görünür:</span><span class="sxs-lookup"><span data-stu-id="625a3-331">Clicking the first task reveals the following task configuration:</span></span>

![Yayın işlem hattı dağıtım görevini gösteren ekran görüntüsü](media/cicd/release-definition-task1.png)

<span data-ttu-id="625a3-333">Azure aboneliği, hizmet türü, Web uygulaması adı, kaynak grubu ve dağıtım yuvası dağıtım görevinde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="625a3-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="625a3-334">**Package veya Folder** metin kutusu Ayıklanacak ve *\<unique_number\> MyWebApp* Web uygulamasının *hazırlama* yuvasına dağıtılacak *. zip* dosya yolunu barındırır.</span><span class="sxs-lookup"><span data-stu-id="625a3-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="625a3-335">Yuva takas görevine tıkladığınızda aşağıdaki görev yapılandırması görünür:</span><span class="sxs-lookup"><span data-stu-id="625a3-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![Yayın ardışık düzen yuvası değiştirme görevini gösteren ekran görüntüsü](media/cicd/release-definition-task2.png)

<span data-ttu-id="625a3-337">Abonelik, kaynak grubu, hizmet türü, Web uygulaması adı ve dağıtım yuvası ayrıntıları sağlanır.</span><span class="sxs-lookup"><span data-stu-id="625a3-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="625a3-338">**Üretimle Değiştir** onay kutusu işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="625a3-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="625a3-339">Sonuç olarak, *hazırlama* yuvasına dağıtılan bitler üretim ortamına değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="625a3-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="625a3-340">Ek okuma</span><span class="sxs-lookup"><span data-stu-id="625a3-340">Additional reading</span></span>

* [<span data-ttu-id="625a3-341">Azure Pipelines ile ilk işlem hattınızı oluşturma</span><span class="sxs-lookup"><span data-stu-id="625a3-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="625a3-342">Derleme ve .NET Core projesi</span><span class="sxs-lookup"><span data-stu-id="625a3-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="625a3-343">Azure Pipelines bir Web uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="625a3-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
