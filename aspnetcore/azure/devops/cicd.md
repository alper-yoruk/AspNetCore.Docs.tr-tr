---
title: Sürekli tümleştirme ve dağıtım - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps'lerde sürekli tümleştirme ve dağıtım
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655836"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="45528-103">Sürekli tümleştirme ve dağıtım</span><span class="sxs-lookup"><span data-stu-id="45528-103">Continuous integration and deployment</span></span>

<span data-ttu-id="45528-104">Önceki bölümde, Basit Özet Okuyucu uygulaması için yerel bir Git deposu oluşturdunuz.</span><span class="sxs-lookup"><span data-stu-id="45528-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="45528-105">Bu bölümde, bu kodu bir GitHub deposunda yayımlar ve Azure Pipelines kullanarak bir Azure DevOps Hizmetleri ardışık hattı oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="45528-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="45528-106">Boru hattı, uygulamanın sürekli olarak oluşturulmasını ve dağıtımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="45528-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="45528-107">GitHub deposuna yapılan herhangi bir taahhüt, bir yapıyı ve Azure Web Uygulaması'nın evreleme yuvasına dağıtımı tetikler.</span><span class="sxs-lookup"><span data-stu-id="45528-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="45528-108">Bu bölümde, aşağıdaki görevleri tamamlayaceksiniz:</span><span class="sxs-lookup"><span data-stu-id="45528-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="45528-109">Uygulamanın kodunu GitHub'da yayımla</span><span class="sxs-lookup"><span data-stu-id="45528-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="45528-110">Yerel Git dağıtımını kesme</span><span class="sxs-lookup"><span data-stu-id="45528-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="45528-111">Azure DevOps kuruluşu oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="45528-112">Azure DevOps Hizmetlerinde ekip projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="45528-113">Derleme tanımı oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-113">Create a build definition</span></span>
* <span data-ttu-id="45528-114">Yayın işlem hattı oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-114">Create a release pipeline</span></span>
* <span data-ttu-id="45528-115">GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma</span><span class="sxs-lookup"><span data-stu-id="45528-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="45528-116">Azure Ardışık Hatlar ardışık hattını inceleyin</span><span class="sxs-lookup"><span data-stu-id="45528-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="45528-117">Uygulamanın kodunu GitHub'da yayımla</span><span class="sxs-lookup"><span data-stu-id="45528-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="45528-118">Tarayıcı penceresi açın ve `https://github.com`''ye gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="45528-119">Üstbilgideki **+** açılır bırakmayı tıklatın ve **Yeni depoyu**seçin:</span><span class="sxs-lookup"><span data-stu-id="45528-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub Yeni Depo seçeneği](media/cicd/github-new-repo.png)

1. <span data-ttu-id="45528-121">**Sahibi** açılır durumda hesabınızı seçin ve **Depo adı** metin kutusuna basit *özet okuyucu* girin.</span><span class="sxs-lookup"><span data-stu-id="45528-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="45528-122">Oluştur **deposu düğmesini** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="45528-123">Yerel makinenizin komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="45528-123">Open your local machine's command shell.</span></span> <span data-ttu-id="45528-124">*Basit besleme okuyucu* Git deposunun depolandığı dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="45528-125">Varolan *orijin* uzaktan kumandasını yukarı akışa yeniden *adlandırın.*</span><span class="sxs-lookup"><span data-stu-id="45528-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="45528-126">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="45528-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="45528-127">GitHub'daki deponun kopyanıza işaret eden yeni bir *başlangıç* uzaktan kumandası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="45528-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="45528-128">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="45528-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="45528-129">Yerel Git deponuzu yeni oluşturulan GitHub deposunda yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="45528-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="45528-130">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="45528-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="45528-131">Tarayıcı penceresi açın ve `https://github.com/<GitHub_username>/simple-feed-reader/`''ye gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="45528-132">Kodunuzun GitHub deposunda göründüğünü doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="45528-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="45528-133">Yerel Git dağıtımını kesme</span><span class="sxs-lookup"><span data-stu-id="45528-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="45528-134">Aşağıdaki adımlarla yerel Git dağıtımını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="45528-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="45528-135">Azure Denetim Hatları (Azure DevOps hizmeti) bu işlevselliğin hem yerini alır hem de genişletir.</span><span class="sxs-lookup"><span data-stu-id="45528-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="45528-136">Azure [portalını](https://portal.azure.com/)açın ve *evreleme (mywebapp\<unique_number\>/evreleme)* Web Uygulamasına gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="45528-137">Web Uygulaması, portalın arama kutusuna *evreleme* girerek hızlı bir şekilde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="45528-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![evreleme Web App arama terimi](media/cicd/portal-search-box.png)

1. <span data-ttu-id="45528-139">**Dağıtım Merkezi'ni**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-139">Click **Deployment Center**.</span></span> <span data-ttu-id="45528-140">Yeni bir panel görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-140">A new panel appears.</span></span> <span data-ttu-id="45528-141">Önceki bölümde eklenen yerel Git kaynak denetimi yapılandırmasını kaldırmak için **Bağlantıyı Kesme'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="45528-142">**Evet** düğmesini tıklatarak kaldırma işlemini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="45528-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="45528-143">>App Service *unique_number<mywebapp'a* gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="45528-144">Bir hatırlatma olarak, portalın arama kutusu, Uygulama Hizmetini hızla bulmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="45528-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="45528-145">**Dağıtım Merkezi'ni**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-145">Click **Deployment Center**.</span></span> <span data-ttu-id="45528-146">Yeni bir panel görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-146">A new panel appears.</span></span> <span data-ttu-id="45528-147">Önceki bölümde eklenen yerel Git kaynak denetimi yapılandırmasını kaldırmak için **Bağlantıyı Kesme'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="45528-148">**Evet** düğmesini tıklatarak kaldırma işlemini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="45528-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="45528-149">Azure DevOps kuruluşu oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="45528-150">Bir tarayıcı açın ve [Azure DevOps kuruluş oluşturma sayfasına](https://go.microsoft.com/fwlink/?LinkId=307137)gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="45528-151">Azure DevOps kuruluşunuza erişmek için URL oluşturmak için **unutulmaz bir ad metin** kutusu seçin'e benzersiz bir ad yazın.</span><span class="sxs-lookup"><span data-stu-id="45528-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="45528-152">Kod GitHub deposunda barındırılan git **radyo** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="45528-153">Devam **et** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-153">Click the **Continue** button.</span></span> <span data-ttu-id="45528-154">Kısa bir bekleyişten sonra *MyFirstProject*adında bir hesap ve ekip projesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="45528-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps organizasyon oluşturma sayfası](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="45528-156">Azure DevOps kuruluşunun ve projesinin kullanıma hazır olduğunu belirten onay e-postasını açın.</span><span class="sxs-lookup"><span data-stu-id="45528-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="45528-157">**Projenizi Başlat** düğmesini tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-157">Click the **Start your project** button:</span></span>

    ![Proje düğmenizi başlatın](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="45528-159">Bir tarayıcı \* \<account_name\>.visualstudio.com\*açılır.</span><span class="sxs-lookup"><span data-stu-id="45528-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="45528-160">Projenin DevOps ardışık hattını yapılandırmaya başlamak için *MyFirstProject* bağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="45528-161">Azure Ardışık Hatlar ardışık hattını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="45528-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="45528-162">Tamamlanması gereken üç farklı adım vardır.</span><span class="sxs-lookup"><span data-stu-id="45528-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="45528-163">Aşağıdaki üç bölümdeki adımların tamamlanması, operasyonel bir DevOps boru hattıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="45528-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="45528-164">Azure DevOps'lerin GitHub deposuna erişimini verme</span><span class="sxs-lookup"><span data-stu-id="45528-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="45528-165">Harici **bir depo akordeonundan kodu genişletin veya oluşturun.**</span><span class="sxs-lookup"><span data-stu-id="45528-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="45528-166">Kurulum **Yapı** düğmesini tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-166">Click the **Setup Build** button:</span></span>

    ![Kurulum Yap düğmesi](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="45528-168">**Kaynak** seç bölümünden **GitHub** seçeneğini seçin:</span><span class="sxs-lookup"><span data-stu-id="45528-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Kaynak seçin - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="45528-170">Azure DevOps'ların GitHub deponuza erişebilmeleri için yetkilendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="45528-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="45528-171">**Bağlantı adı** metin kutusuna GitHub_username *> GitHub bağlantısı<* girin.</span><span class="sxs-lookup"><span data-stu-id="45528-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="45528-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="45528-172">For example:</span></span>

    ![GitHub bağlantı adı](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="45528-174">GitHub hesabınızda iki faktörlü kimlik doğrulama sısağlanırsa, kişisel erişim belirteci gereklidir.</span><span class="sxs-lookup"><span data-stu-id="45528-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="45528-175">Bu durumda, **GitHub kişisel erişim belirteci bağlantısıyla Yetkilendirme'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="45528-176">Yardım için [resmi GitHub kişisel erişim belirteci oluşturma yönergelerine](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) bakın.</span><span class="sxs-lookup"><span data-stu-id="45528-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="45528-177">İzinlerin yalnızca *repo* kapsamı gereklidir.</span><span class="sxs-lookup"><span data-stu-id="45528-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="45528-178">Aksi takdirde, **OAuth düğmesini kullanarak Yetkilendirme'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="45528-179">İstendiğinde GitHub hesabınızda oturum açın.</span><span class="sxs-lookup"><span data-stu-id="45528-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="45528-180">Ardından Azure DevOps kuruluşunuza erişim izni vermek için Yetki'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="45528-181">Başarılı olursa, yeni bir hizmet bitiş noktası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="45528-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="45528-182">Depo düğmesinin yanındaki elips **düğmesini** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="45528-183">Listeden *<GitHub_username>/basit besleme okuyucu* deposunu seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="45528-184">**Seç** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="45528-185">El ile ve **zamanlanmış yapılar** açılır bırakmak için Varsayılan daldan *ana* dalı seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="45528-186">Devam **et** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-186">Click the **Continue** button.</span></span> <span data-ttu-id="45528-187">Şablon seçim sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="45528-188">Yapı tanımını oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-188">Create the build definition</span></span>

1. <span data-ttu-id="45528-189">Şablon seçim sayfasından, arama kutusuna *ASP.NET Core* girin:</span><span class="sxs-lookup"><span data-stu-id="45528-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![şablon sayfasında ASP.NET Çekirdek arama](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="45528-191">Şablon arama sonuçları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-191">The template search results appear.</span></span> <span data-ttu-id="45528-192">**ASP.NET Core** şablonuna basın ve **Uygula** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="45528-193">Yapı tanımının **Görevler** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="45528-194">**Tetikleyiciler** sekmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="45528-195">Sürekli **tümleştirmeyi etkinleştir** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="45528-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="45528-196">Branch **filtreleri** bölümünün altında, **Tür** açılır bırakma nın *Dahil*olacak şekilde ayarlı olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="45528-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="45528-197">Şube **belirtimini** *ana*olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="45528-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Sürekli tümleştirme ayarlarını etkinleştirme](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="45528-199">Bu ayarlar, herhangi bir değişiklik GitHub deposunun *ana* dalına itildiğinde bir yapının tetiklenebilmelidir.</span><span class="sxs-lookup"><span data-stu-id="45528-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="45528-200">Sürekli tümleştirme, [GitHub'daki Commit değişikliklerinde](#commit-changes-to-github-and-automatically-deploy-to-azure) sınanıyor ve otomatik olarak Azure bölümüne dağıtılıyor.</span><span class="sxs-lookup"><span data-stu-id="45528-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="45528-201">& **sırayı kaydet** düğmesini tıklatın ve **Kaydet** seçeneğini seçin:</span><span class="sxs-lookup"><span data-stu-id="45528-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Kaydet düğmesi](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="45528-203">Aşağıdaki modal iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="45528-203">The following modal dialog appears:</span></span>

    ![Yapı tanımını kaydet - modal iletişim kutusu](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="45528-205">Varsayılan klasörü *\\*kullanın ve **Kaydet** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="45528-206">Sürüm ardışık hattını oluşturma</span><span class="sxs-lookup"><span data-stu-id="45528-206">Create the release pipeline</span></span>

1. <span data-ttu-id="45528-207">Takım projenizin **Sürümler** sekmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="45528-208">Yeni **ardışık hat lar** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-208">Click the **New pipeline** button.</span></span>

    ![Sürümler sekmesi - Yeni tanım düğmesi](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="45528-210">Şablon seçim bölmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="45528-211">Şablon seçim sayfasından, arama kutusuna *Uygulama Hizmeti* girin:</span><span class="sxs-lookup"><span data-stu-id="45528-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Yayın boru hattı şablonu arama kutusu](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="45528-213">Şablon arama sonuçları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-213">The template search results appear.</span></span> <span data-ttu-id="45528-214">**Slot şablonuyla Azure Uygulama Hizmeti Dağıtımı'nın** üzerine tıklayın ve **Uygula** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="45528-215">Sürüm ardışık **alanının Ardışık Hatlar sekmesi** görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Sürüm ardışık hatlar ardışık hatlar sekmesi](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="45528-217">**Artefakt** lar **kutusundaekle** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="45528-218">**Yapı ekle** paneli görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="45528-218">The **Add artifact** panel appears:</span></span>

    ![Sürüm ardışık hattı - Yapı paneli ekle](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="45528-220">**Kaynak türü** bölümünden **Yapı** döşemesini seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="45528-221">Bu tür, sürüm ardışık hattının yapı tanımına bağlanmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="45528-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="45528-222">**Proje** açılır tarafından *MyFirstProject'i* seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="45528-223">**Kaynak (Yapı tanımı)** açılır tarafından yapı tanım adını, *MyFirstProject-ASP.NET Core-CI'yi*seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="45528-224">**Varsayılan sürüm** açılır tarafından *En Son'u* seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="45528-225">Bu seçenek, yapı tanımının en son çalıştırımı tarafından üretilen yapıları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="45528-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="45528-226">**Kaynak takma ad** metin kutusundaki metni *Drop*ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="45528-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="45528-227">**Ekle** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-227">Click the **Add** button.</span></span> <span data-ttu-id="45528-228">Değişiklikler görüntülemek için **Eserler** bölümü güncellenir.</span><span class="sxs-lookup"><span data-stu-id="45528-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="45528-229">Sürekli dağıtımları etkinleştirmek için yıldırım simgesini tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Yayın boru hattı Artifakı - yıldırım simgesi](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="45528-231">Bu seçenek etkinleştirildiğinde, her yeni yapı kullanılabilir olduğunda bir dağıtım oluşur.</span><span class="sxs-lookup"><span data-stu-id="45528-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="45528-232">**Sürekli dağıtım tetikleyici** paneli sağda görünür.</span><span class="sxs-lookup"><span data-stu-id="45528-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="45528-233">Özelliği etkinleştirmek için geçiş düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="45528-234">**Çekme isteği tetikleyicisini**etkinleştirmek gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="45528-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="45528-235">**Yap dalı filtreleri** bölümünde **açılan** ekle'yi tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="45528-236">Yapı **Tanımı'nın varsayılan dal** seçeneğini seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="45528-237">Bu filtre, sürümün yalnızca GitHub deposunun *ana* dalından bir yapı için tetiklemeye neden olur.</span><span class="sxs-lookup"><span data-stu-id="45528-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="45528-238">**Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="45528-238">Click the **Save** button.</span></span> <span data-ttu-id="45528-239">Ortaya çıkan **Kaydet** modal iletişim kutusunda **Tamam** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="45528-240">Çevre **1** kutusunu tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="45528-241">Sağda bir **Çevre** paneli görünür.</span><span class="sxs-lookup"><span data-stu-id="45528-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="45528-242">**Çevre adı** textbox'taki *Çevre 1* metnini *Üretim*olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="45528-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Yayın ardışık hattı - Çevre adı textbox](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="45528-244">**Üretim** **kutusunda1 faz, 2 görev** bağlantısını tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Yayın boru hattı - Üretim ortamı link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="45528-246">Ortamın **Görevler** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="45528-247">Slot görevi **için Azure Uygulama Hizmetini Dağıt'ı** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="45528-248">Ayarları sağdaki panelde görünür.</span><span class="sxs-lookup"><span data-stu-id="45528-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="45528-249">Azure abonelik açılır tarafından Uygulama Hizmetiile ilişkili Azure **aboneliğini** seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="45528-250">Seçildikten sonra **Yetkilendirme** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="45528-251">**Uygulama türü** açılır sayfasından *Web Uygulamasını* seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="45528-252">**Uygulama hizmeti adı** açılır *unique_number/>mywebapp/<'ı* seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="45528-253">**Kaynak grubu** açılır tarafından *AzureTutorial'i* seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="45528-254">**Yuva** açılır yerden *evreleme* seçin.</span><span class="sxs-lookup"><span data-stu-id="45528-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="45528-255">**Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="45528-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="45528-256">Varsayılan sürüm ardışık ad üzerinde gezinmek.</span><span class="sxs-lookup"><span data-stu-id="45528-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="45528-257">Bunu yapmak için kalem simgesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="45528-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="45528-258">Ad olarak *MyFirstProject-ASP.NET Core-CD* kullanın.</span><span class="sxs-lookup"><span data-stu-id="45528-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Sürüm ardışık adı](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="45528-260">**Kaydet** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="45528-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="45528-261">GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma</span><span class="sxs-lookup"><span data-stu-id="45528-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="45528-262">*SimpleFeedReader.sln'i* Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="45528-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="45528-263">Çözüm Gezgini'nde, *Sayfalar\Index.cshtml'i*açın.</span><span class="sxs-lookup"><span data-stu-id="45528-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="45528-264">'ye `<h2>Simple Feed Reader - V4</h2>`değiştirin. `<h2>Simple Feed Reader - V3</h2>`</span><span class="sxs-lookup"><span data-stu-id="45528-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="45528-265">Uygulamayı oluşturmak için **Ctrl**+**Shift**+**B** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="45528-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="45528-266">Dosyayı GitHub deposuna ada.</span><span class="sxs-lookup"><span data-stu-id="45528-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="45528-267">Visual Studio'nun *Takım Gezgini* sekmesinde **Değişiklikler** sayfasını kullanın veya yerel makinenin komut kabuğunu kullanarak aşağıdakileri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="45528-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="45528-268">*Ana* daldaki değişikliği GitHub deponuzun *başlangıç* uzak merkezine itin:</span><span class="sxs-lookup"><span data-stu-id="45528-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="45528-269">Commit, GitHub deposunun *ana* dalında görünür:</span><span class="sxs-lookup"><span data-stu-id="45528-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![GitHub ana şube taahhüt](media/cicd/github-commit.png)

    <span data-ttu-id="45528-271">Yapı tanımının **Tetikleyiciler** sekmesinde sürekli tümleştirme etkinleştirildiğinden, yapı tetiklenir:</span><span class="sxs-lookup"><span data-stu-id="45528-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![sürekli entegrasyon sağlamak](media/cicd/enable-ci.png)

1. <span data-ttu-id="45528-273">Azure DevOps Hizmetleri'nde **Azure Ardışık Hatları** > **Oluşturur** sayfasının **Sıralı** sekmesine gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="45528-274">Sıralanan yapı, yapıyı tetikleyen dalı ve işlemeyi gösterir:</span><span class="sxs-lookup"><span data-stu-id="45528-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![sıraya alı](media/cicd/build-queued.png)

1. <span data-ttu-id="45528-276">Yapı başarılı olduktan sonra Azure'a bir dağıtım gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="45528-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="45528-277">Tarayıcıdaki uygulamaya gidin.</span><span class="sxs-lookup"><span data-stu-id="45528-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="45528-278">"V4" metninin başlıkta göründüğüne dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="45528-278">Notice that the "V4" text appears in the heading:</span></span>

    ![güncelleştirilmiş uygulama](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="45528-280">Azure Ardışık Hatlar ardışık hattını inceleyin</span><span class="sxs-lookup"><span data-stu-id="45528-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="45528-281">Derleme tanımı</span><span class="sxs-lookup"><span data-stu-id="45528-281">Build definition</span></span>

<span data-ttu-id="45528-282">*Core-CI*adı ile bir yapı tanımı MyFirstProject-ASP.NET oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="45528-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="45528-283">Tamamlandıktan sonra, yapı yayımlanacak varlıkları içeren bir *.zip* dosyası üretir.</span><span class="sxs-lookup"><span data-stu-id="45528-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="45528-284">Sürüm ardışık alanı bu varlıkları Azure'a dağır.</span><span class="sxs-lookup"><span data-stu-id="45528-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="45528-285">Yapı tanımının **Görevler** sekmesi, kullanılan tek tek adımları listeler.</span><span class="sxs-lookup"><span data-stu-id="45528-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="45528-286">Beş yapı görevi vardır.</span><span class="sxs-lookup"><span data-stu-id="45528-286">There are five build tasks.</span></span>

![tanım görevleri oluşturma](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="45528-288">**Geri Yükleme** &mdash; `dotnet restore` Uygulamanın NuGet paketlerini geri yüklemek için komutu yürütür.</span><span class="sxs-lookup"><span data-stu-id="45528-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="45528-289">Kullanılan varsayılan paket akışı nuget.org.</span><span class="sxs-lookup"><span data-stu-id="45528-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="45528-290">**Build** &mdash; Uygulamanın `dotnet build --configuration release` kodunu derlemek için komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="45528-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="45528-291">Bu `--configuration` seçenek, kodun üretim ortamına dağıtım için uygun olan en iyi leştirilmiş sürümünü oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="45528-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="45528-292">Yapı tanımının **Değişkenler** sekmesinde, örneğin hata ayıklama yapılandırması *gerekiyorsa, Yapı Yapılandırma* değişkenini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="45528-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="45528-293">**Test,** &mdash; uygulamanın `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` birim testlerini çalıştırmak için komutu yürütür.</span><span class="sxs-lookup"><span data-stu-id="45528-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="45528-294">Birim testleri `**/*Tests/*.csproj` glob deseni eşleşen herhangi bir C# projesi içinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="45528-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="45528-295">Test sonuçları opsiyonun belirttiği yerde *bir .trx* dosyasına `--results-directory` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="45528-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="45528-296">Herhangi bir test başarısız olursa, yapı başarısız olur ve dağıtılmaz.</span><span class="sxs-lookup"><span data-stu-id="45528-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="45528-297">Birim testlerinin çalıştığını doğrulamak için *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* siyi testlerden birini kasıtlı olarak kırmak için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="45528-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="45528-298">Örneğin, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` yöntemde değiştirin. `Returns_News_Stories_Given_Valid_Uri`</span><span class="sxs-lookup"><span data-stu-id="45528-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="45528-299">Değişikliği GitHub'a bağlayıp itin.</span><span class="sxs-lookup"><span data-stu-id="45528-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="45528-300">Yapı tetiklenir ve başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="45528-300">The build is triggered and fails.</span></span> <span data-ttu-id="45528-301">Yapı ardışık yapı durumu **başarısız olacak**şekilde değişir.</span><span class="sxs-lookup"><span data-stu-id="45528-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="45528-302">Değişikliği geri çevirin, taahhüt edin ve tekrar itin.</span><span class="sxs-lookup"><span data-stu-id="45528-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="45528-303">Yapı başarılı oldu.</span><span class="sxs-lookup"><span data-stu-id="45528-303">The build succeeds.</span></span>

1. <span data-ttu-id="45528-304">**Yayımlama,** &mdash; `dotnet publish --configuration release --output <local_path_on_build_agent>` dağıtılacak yapılarla birlikte *bir .zip* dosyası oluşturmak için komutu yürütür.</span><span class="sxs-lookup"><span data-stu-id="45528-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="45528-305">`--output` *Seçenek,.zip* dosyasının yayımlama konumunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="45528-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="45528-306">Bu konum, önceden [tanımlanmış](/azure/devops/pipelines/build/variables) bir `$(build.artifactstagingdirectory)`değişken in '' adlı bir değişken geçerek belirtilir.</span><span class="sxs-lookup"><span data-stu-id="45528-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="45528-307">Bu değişken, yapı aracısı üzerinde *c:\agent\_work\1\a*gibi yerel bir yola genişler.</span><span class="sxs-lookup"><span data-stu-id="45528-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="45528-308">**Yayımlama Artefakt,** &mdash; **Yayımlama** görevi tarafından üretilen *.zip* dosyasını yayımlar.</span><span class="sxs-lookup"><span data-stu-id="45528-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="45528-309">Görev, *.zip* dosya konumunu önceden tanımlanmış değişken `$(build.artifactstagingdirectory)`olan bir parametre olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="45528-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="45528-310">*.zip* dosyası *damla*adlı bir klasör olarak yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="45528-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="45528-311">Yapı geçmişinin tanımıyla birlikte görüntülenebilmek için yapı tanımının **Özet** bağlantısını tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Yapı tanım geçmişini gösteren ekran görüntüsü](media/cicd/build-definition-summary.png)

<span data-ttu-id="45528-313">Ortaya çıkan sayfada, benzersiz yapı numarasına karşılık gelen bağlantıyı tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Yapı tanımı özet sayfasını gösteren ekran görüntüsü](media/cicd/build-definition-completed.png)

<span data-ttu-id="45528-315">Bu özel yapının bir özeti görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="45528-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="45528-316">**Yapılar** sekmesini tıklatın ve yapı tarafından üretilen *açılan* klasörün listelenmiş olduğunu fark edin:</span><span class="sxs-lookup"><span data-stu-id="45528-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Yapı tanımı yapılarını gösteren ekran görüntüsü - drop klasörü](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="45528-318">Yayımlanmış eserleri incelemek için **İndir** ve **Araştır** bağlantılarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="45528-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="45528-319">Yayın işlem hattı</span><span class="sxs-lookup"><span data-stu-id="45528-319">Release pipeline</span></span>

<span data-ttu-id="45528-320">*Core-CD*adı ile bir sürüm boru hattı oluşturuldu MyFirstProject-ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="45528-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Sürüm ardışık genel görünümünü gösteren ekran görüntüsü](media/cicd/release-definition-overview.png)

<span data-ttu-id="45528-322">Sürüm ardışık iki ana bileşeni **Eserler** ve **Ortamlar**vardır.</span><span class="sxs-lookup"><span data-stu-id="45528-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="45528-323">**Eserler** bölümündeki kutuyu tıklattığınızda aşağıdaki panel ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="45528-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Sürüm ardışık yapıtlarını gösteren ekran görüntüsü](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="45528-325">**Kaynak (Yapı tanımı)** değeri, bu sürüm ardışık hattının bağlı olduğu yapı tanımını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="45528-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="45528-326">Yapı tanımının başarılı bir şekilde çalıştırılması yla üretilen *.zip* dosyası, Azure'a dağıtım için *Üretim* ortamına sağlanır.</span><span class="sxs-lookup"><span data-stu-id="45528-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="45528-327">Sürüm ardışık işleri görmek için *Üretim* ortamı kutusundaki *1 faz, 2 görev* bağlantısını tıklatın:</span><span class="sxs-lookup"><span data-stu-id="45528-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Sürüm ardışık iş aksama görevlerini gösteren ekran görüntüsü](media/cicd/release-definition-tasks.png)

<span data-ttu-id="45528-329">Sürüm ardışık alanı iki görevden oluşur: *Azure Uygulama Hizmetini Yuvaya Dağıtın* ve *Azure Uygulama Hizmetini Yönetin - Yuva Değiştirme*.</span><span class="sxs-lookup"><span data-stu-id="45528-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="45528-330">İlk görevi tıklattığınızda aşağıdaki görev yapılandırması ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="45528-330">Clicking the first task reveals the following task configuration:</span></span>

![Sürüm ardışık alan dağıtım görevini gösteren ekran görüntüsü](media/cicd/release-definition-task1.png)

<span data-ttu-id="45528-332">Azure aboneliği, hizmet türü, web uygulaması adı, kaynak grubu ve dağıtım yuvası dağıtım görevinde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="45528-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="45528-333">**Paket veya klasör** textbox ayıklanacak ve \*mywebapp\<unique_number\> \* web *uygulamasının evreleme* yuvasına dağıtılacak *.zip* dosya yolunu tutar.</span><span class="sxs-lookup"><span data-stu-id="45528-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="45528-334">Yuva değiştirme görevini tıklattığınızda aşağıdaki görev yapılandırması ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="45528-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Sürüm ardışık ardışık yuva değiştirme görevini gösteren ekran görüntüsü](media/cicd/release-definition-task2.png)

<span data-ttu-id="45528-336">Abonelik, kaynak grubu, hizmet türü, web uygulaması adı ve dağıtım yuvası ayrıntıları sağlanır.</span><span class="sxs-lookup"><span data-stu-id="45528-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="45528-337">**Üretimle Takas** onay kutusu işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="45528-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="45528-338">Sonuç olarak, *evreleme* yuvasına dağıtılan bitler üretim ortamına dönüştürülr.</span><span class="sxs-lookup"><span data-stu-id="45528-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="45528-339">Ek okuma</span><span class="sxs-lookup"><span data-stu-id="45528-339">Additional reading</span></span>

* [<span data-ttu-id="45528-340">Azure Ardışık Hatları ile ilk ardışık sisteminizi oluşturun</span><span class="sxs-lookup"><span data-stu-id="45528-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="45528-341">Yapı ve .NET Core projesi</span><span class="sxs-lookup"><span data-stu-id="45528-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="45528-342">Azure Pipelines ile bir web uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="45528-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
