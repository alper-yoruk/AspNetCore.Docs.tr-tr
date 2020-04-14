---
title: Uygulamayı Uygulama Hizmetine dağıtın - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps'lerin ilk adımı olan Azure Uygulama Hizmetine bir ASP.NET Core uygulaması dağıtın.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: d7ee3e42d320d35c2aaff6e097203c45289ec5b1
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228133"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="f7bca-103">Uygulama Hizmetine uygulama dağıtma</span><span class="sxs-lookup"><span data-stu-id="f7bca-103">Deploy an app to App Service</span></span>

<span data-ttu-id="f7bca-104">[Azure Uygulama Hizmeti,](/azure/app-service/) Azure'un web barındırma platformudur.</span><span class="sxs-lookup"><span data-stu-id="f7bca-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="f7bca-105">Bir web uygulamasını Azure Uygulama Hizmeti'ne dağıtmak el ile veya otomatik bir işlemle yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="f7bca-106">Kılavuzun bu bölümünde, komut satırı kullanılarak el ile veya komut dosyası yla tetiklenebilen veya Visual Studio kullanılarak el ile tetiklenen dağıtım yöntemleri anlatılır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="f7bca-107">Bu bölümde, aşağıdaki görevleri yerine getirirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f7bca-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="f7bca-108">Örnek uygulamayı indirin ve oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-108">Download and build the sample app.</span></span>
* <span data-ttu-id="f7bca-109">Azure Bulut Kabuğu'nu kullanarak bir Azure Uygulama Hizmeti Web Uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="f7bca-110">Örnek uygulamayı Git'i kullanarak Azure'a dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="f7bca-111">Visual Studio'yu kullanarak uygulamaya bir değişiklik dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="f7bca-112">Web uygulamasına bir evreleme yuvası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="f7bca-113">Evreleme yuvasına bir güncelleştirme dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="f7bca-114">Hazırlama ve üretim yuvalarını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="f7bca-115">Uygulamayı indirin ve test edin</span><span class="sxs-lookup"><span data-stu-id="f7bca-115">Download and test the app</span></span>

<span data-ttu-id="f7bca-116">Bu kılavuzda kullanılan uygulama önceden oluşturulmuş bir ASP.NET Core uygulaması, [Basit Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="f7bca-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="f7bca-117">RSS/Atom akışı almak ve `Microsoft.SyndicationFeed.ReaderWriter` haber öğelerini bir listede görüntülemek için API'yi kullanan bir Razor Pages uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="f7bca-118">Kodu gözden geçirmekten çekinmeyin, ancak bu uygulamanın özel bir şey olmadığını anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="f7bca-119">Bu sadece açıklayıcı amaçlar için basit bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="f7bca-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="f7bca-120">Komut kabuğundan kodu indirin, projeyi oluşturun ve aşağıdaki gibi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="f7bca-121">*Not: Linux/macOS kullanıcıları yollar için uygun değişiklikler yapmalıdır, örneğin, geri eğik çizgi yerine ileri eğik çizgi ()`/`kullanarak .`\`*</span><span class="sxs-lookup"><span data-stu-id="f7bca-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="f7bca-122">Kodu yerel makinenizdeki bir klasöre kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="f7bca-123">Çalışma klasörünüzü oluşturulan *basit özet okuyucu* klasörüne değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="f7bca-124">Paketleri geri yükleyin ve çözümü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="f7bca-125">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Dotnet çalıştır komutu başarılı](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="f7bca-127">Bir tarayıcıyı açın ve `http://localhost:5000` dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="f7bca-128">Uygulama, bir sendikasyon akışı URL'si yazmanıza veya yapıştırmanıza ve haber öğelerinin listesini görüntülemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![RSS akışının içeriğini görüntüleyen uygulama](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="f7bca-130">Uygulamanın doğru çalıştığından emin olduktan sonra, komut kabuğundaki **Ctrl**+**C** tuşuna basarak uygulamayı kapatın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="f7bca-131">Azure App Service Web Uygulamasını Oluşturun</span><span class="sxs-lookup"><span data-stu-id="f7bca-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="f7bca-132">Uygulamayı dağıtmak için bir Uygulama Hizmeti Web [Uygulaması](/azure/app-service/app-service-web-overview)oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="f7bca-133">Web Uygulaması oluşturulduktan sonra, Git'i kullanarak yerel makinenizden dağıtacaksınız.</span><span class="sxs-lookup"><span data-stu-id="f7bca-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="f7bca-134">[Azure Cloud Shell](https://shell.azure.com/bash)'de oturum açın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="f7bca-135">Not: İlk kez oturum açtığınızda, Cloud Shell yapılandırma dosyaları için bir depolama hesabı oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="f7bca-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="f7bca-136">Varsayılanları kabul edin veya benzersiz bir ad sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="f7bca-137">Aşağıdaki adımlar için Bulut Kabuğu'nu kullanın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="f7bca-138">a.</span><span class="sxs-lookup"><span data-stu-id="f7bca-138">a.</span></span> <span data-ttu-id="f7bca-139">Web uygulamanızın adını depolamak için bir değişken bildirin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="f7bca-140">Varsayılan URL'de kullanılabilmesi için ad benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="f7bca-141">Adı `$RANDOM` oluşturmak için Bash işlevini kullanmak benzersizliği garanti `webappname99999`eder ve biçimde sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="f7bca-142">b.</span><span class="sxs-lookup"><span data-stu-id="f7bca-142">b.</span></span> <span data-ttu-id="f7bca-143">Bir kaynak grubu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-143">Create a resource group.</span></span> <span data-ttu-id="f7bca-144">Kaynak grupları, grup olarak yönetilecek Azure kaynaklarını toplamak için bir araç sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7bca-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="f7bca-145">Komut `az` Azure [CLI'yi](/cli/azure/)çağırır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="f7bca-146">CLI yerel olarak çalıştırılabilir, ancak Bulut Kabuğu'nda kullanmak zamandan ve yapılandırmadan tasarruf sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7bca-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="f7bca-147">c.</span><span class="sxs-lookup"><span data-stu-id="f7bca-147">c.</span></span> <span data-ttu-id="f7bca-148">S1 katmanında bir Uygulama Hizmeti planı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="f7bca-149">Uygulama Hizmeti planı, aynı fiyatlandırma katmanını paylaşan web uygulamalarının gruplandırılmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="f7bca-150">S1 katmanı ücretsiz değildir, ancak evreleme yuvaları özelliği için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="f7bca-151">d.</span><span class="sxs-lookup"><span data-stu-id="f7bca-151">d.</span></span> <span data-ttu-id="f7bca-152">Aynı kaynak grubunda Uygulama Hizmeti planını kullanarak web uygulaması kaynağını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="f7bca-153">e.</span><span class="sxs-lookup"><span data-stu-id="f7bca-153">e.</span></span> <span data-ttu-id="f7bca-154">Dağıtım kimlik bilgilerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-154">Set the deployment credentials.</span></span> <span data-ttu-id="f7bca-155">Bu dağıtım kimlik bilgileri aboneliğinizdeki tüm web uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="f7bca-156">Kullanıcı adında özel karakterler kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="f7bca-157">f.</span><span class="sxs-lookup"><span data-stu-id="f7bca-157">f.</span></span> <span data-ttu-id="f7bca-158">Web uygulamasını yerel Git'den gelen dağıtımları kabul etmek ve *Git dağıtım URL'sini*görüntülemek için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="f7bca-159">**Başvuru için bu URL'yi daha sonra not edin.**</span><span class="sxs-lookup"><span data-stu-id="f7bca-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="f7bca-160">g.</span><span class="sxs-lookup"><span data-stu-id="f7bca-160">g.</span></span> <span data-ttu-id="f7bca-161">Web *uygulaması URL'sini*görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-161">Display the *web app URL*.</span></span> <span data-ttu-id="f7bca-162">Boş web uygulamasını görmek için bu URL'ye göz atın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="f7bca-163">**Başvuru için bu URL'yi daha sonra not edin.**</span><span class="sxs-lookup"><span data-stu-id="f7bca-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="f7bca-164">Yerel makinenizde komut kabuğu nu kullanarak, web uygulamasının proje klasörüne gidin (örneğin, `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="f7bca-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="f7bca-165">Git'i dağıtım URL'sine itmek için ayarlamak için aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f7bca-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="f7bca-166">a.</span><span class="sxs-lookup"><span data-stu-id="f7bca-166">a.</span></span> <span data-ttu-id="f7bca-167">Uzak URL'yi yerel depoya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="f7bca-168">b.</span><span class="sxs-lookup"><span data-stu-id="f7bca-168">b.</span></span> <span data-ttu-id="f7bca-169">Yerel *ana* dalı *azure-prod* uzaktan kumandanın *ana* dalına itin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="f7bca-170">Daha önce oluşturduğunuz dağıtım kimlik bilgileri için istenirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f7bca-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="f7bca-171">Komut kabuğundaki çıkışı gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-171">Observe the output in the command shell.</span></span> <span data-ttu-id="f7bca-172">Azure, ASP.NET Core uygulamasını uzaktan oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f7bca-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="f7bca-173">Bir tarayıcıda, *Web uygulaması URL'sine* gidin ve uygulamanın oluşturulup dağıtıldığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="f7bca-174">Yerel Git deposuna ek değişiklikler `git commit`yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="f7bca-175">Bu değişiklikler önceki `git push` komutla Azure'a itilir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="f7bca-176">Visual Studio ile Dağıtım</span><span class="sxs-lookup"><span data-stu-id="f7bca-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="f7bca-177">*Not: Bu bölüm yalnızca Windows için geçerlidir. Linux ve macOS kullanıcıları aşağıdaki adım 2'de açıklanan değişikliği yapmalıdır. Dosyayı kaydedin ve değişikliği yerel depoya `git commit`' la birlikte kaydedin. Son olarak, değişikliği `git push`ilk bölümde olduğu gibi , ile itin.*</span><span class="sxs-lookup"><span data-stu-id="f7bca-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="f7bca-178">Uygulama komut kabuğundan zaten dağıtıldı.</span><span class="sxs-lookup"><span data-stu-id="f7bca-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="f7bca-179">Uygulamaya bir güncelleştirme dağıtmak için Visual Studio'nun tümleşik araçlarını kullanalım.</span><span class="sxs-lookup"><span data-stu-id="f7bca-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="f7bca-180">Sahne arkasında, Visual Studio komut satırı araç olarak aynı şeyi gerçekleştirir, ancak Visual Studio tanıdık UI içinde.</span><span class="sxs-lookup"><span data-stu-id="f7bca-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="f7bca-181">*SimpleFeedReader.sln'i* Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="f7bca-182">Çözüm Gezgini'nde, *Sayfalar\Index.cshtml'i*açın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="f7bca-183">'ye `<h2>Simple Feed Reader - V2</h2>`değiştirin. `<h2>Simple Feed Reader</h2>`</span><span class="sxs-lookup"><span data-stu-id="f7bca-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="f7bca-184">Uygulamayı oluşturmak için **Ctrl**+**Shift**+**B** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="f7bca-185">Çözüm Gezgini'nde projeye sağ tıklayın ve **Yayımla'yı**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Sağ tıklatma, Yayımla](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="f7bca-187">Visual Studio yeni bir Uygulama Hizmeti kaynağı oluşturabilir, ancak bu güncelleştirme varolan dağıtım üzerinden yayınlanacaktır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="f7bca-188">**Yayımlama hedef** iletişim kutusunda, soldaki listeden **App Service'i** seçin ve ardından **Varolan'ı seçin'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="f7bca-189">**Yayımla**’ta tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-189">Click **Publish**.</span></span>
6. <span data-ttu-id="f7bca-190">Uygulama **Hizmeti** iletişim kutusunda, Azure aboneliğinizi oluşturmak için kullanılan Microsoft veya Kuruluş hesabının sağ üstte görüntülendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="f7bca-191">Değilse, açılır yere tıklayın ve ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="f7bca-192">Doğru Azure **Aboneliği'nin** seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="f7bca-193">**Görünüm**için **Kaynak Grubu'nü**seçin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="f7bca-194">**AzureTutorial** kaynak grubunu genişletin ve ardından varolan web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="f7bca-195">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-195">Click **OK**.</span></span>

    ![Uygulama Hizmeti Yayımla iletişim kutusunu gösteren ekran görüntüsü](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="f7bca-197">Visual Studio uygulamayı oluşturur ve Azure'a dağır.</span><span class="sxs-lookup"><span data-stu-id="f7bca-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="f7bca-198">Web uygulaması URL'sine göz atın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-198">Browse to the web app URL.</span></span> <span data-ttu-id="f7bca-199">Öğe değişikliğinin `<h2>` canlı olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-199">Validate that the `<h2>` element modification is live.</span></span>

![Başlığı değiştirilen uygulama](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="f7bca-201">Dağıtım yuvaları</span><span class="sxs-lookup"><span data-stu-id="f7bca-201">Deployment slots</span></span>

<span data-ttu-id="f7bca-202">Dağıtım yuvaları, üretimde çalışan uygulamayı etkilemeden değişikliklerin evrelemesine destek verir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="f7bca-203">Uygulamanın aşamalı sürümü bir kalite güvence ekibi tarafından doğrulandıktan sonra, üretim ve hazırlama yuvaları değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="f7bca-204">Evreleme uygulaması bu şekilde üretime yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="f7bca-205">Aşağıdaki adımlar bir evreleme yuvası oluşturmak, bazı değişiklikler dağıtmak ve doğrulamadan sonra üretim ile evreleme yuvası takas.</span><span class="sxs-lookup"><span data-stu-id="f7bca-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="f7bca-206">Azure Bulut [Kabuğu'nda](https://shell.azure.com/bash)oturum açın , daha önce oturum açmamışsa.</span><span class="sxs-lookup"><span data-stu-id="f7bca-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="f7bca-207">Evreleme yuvasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-207">Create the staging slot.</span></span>

    <span data-ttu-id="f7bca-208">a.</span><span class="sxs-lookup"><span data-stu-id="f7bca-208">a.</span></span> <span data-ttu-id="f7bca-209">Ad *evreleme*ile bir dağıtım yuvası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="f7bca-210">b.</span><span class="sxs-lookup"><span data-stu-id="f7bca-210">b.</span></span> <span data-ttu-id="f7bca-211">Hazırlama yuvasını yerel Git'den dağıtım kullanacak ve **hazırlama** dağıtım URL'sini alacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="f7bca-212">**Başvuru için bu URL'yi daha sonra not edin.**</span><span class="sxs-lookup"><span data-stu-id="f7bca-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="f7bca-213">c.</span><span class="sxs-lookup"><span data-stu-id="f7bca-213">c.</span></span> <span data-ttu-id="f7bca-214">Hazırlama yuvasının URL'sini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-214">Display the staging slot's URL.</span></span> <span data-ttu-id="f7bca-215">Boş evreleme yuvasını görmek için URL'ye göz atın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="f7bca-216">**Başvuru için bu URL'yi daha sonra not edin.**</span><span class="sxs-lookup"><span data-stu-id="f7bca-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="f7bca-217">Metin düzenleyicisinde veya Visual Studio'da, öğenin dosyayı `<h2>` okuyup `<h2>Simple Feed Reader - V3</h2>` kaydedilemesi için *Pages/Index.cshtml'i* yeniden değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="f7bca-218">Visual Studio'nun *Takım Gezgini* sekmesindeki **Değişiklikler** sayfasını kullanarak veya yerel makinenin komut kabuğunu kullanarak aşağıdakileri girerek dosyayı yerel Git deposuna işleyin:</span><span class="sxs-lookup"><span data-stu-id="f7bca-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="f7bca-219">Yerel makinenin komut kabuğunu kullanarak, hazırlama dağıtım URL'sini Git uzaktan kumandası olarak ekleyin ve taahhüt edilen değişiklikleri itin:</span><span class="sxs-lookup"><span data-stu-id="f7bca-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="f7bca-220">a.</span><span class="sxs-lookup"><span data-stu-id="f7bca-220">a.</span></span> <span data-ttu-id="f7bca-221">Yerel Git deposuna evreleme için uzak URL ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="f7bca-222">b.</span><span class="sxs-lookup"><span data-stu-id="f7bca-222">b.</span></span> <span data-ttu-id="f7bca-223">Yerel *ana* dalı *azure evreleme* uzaktan kumandasının *ana* dalına itin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="f7bca-224">Azure uygulamayı oluştururken ve dağıtırken bekleyin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="f7bca-225">V3'ün evreleme yuvasına dağıtıldığını doğrulamak için iki tarayıcı penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="f7bca-226">Bir pencerede, orijinal web uygulaması URL'sine gidin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="f7bca-227">Diğer pencerede, evreleme web uygulaması URL'sine gidin.</span><span class="sxs-lookup"><span data-stu-id="f7bca-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="f7bca-228">Üretim URL'si uygulamanın V2'sini hizmet vermektedir.</span><span class="sxs-lookup"><span data-stu-id="f7bca-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="f7bca-229">Evreleme URL'si uygulamanın V3'üne hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="f7bca-229">The staging URL serves V3 of the app.</span></span>

    ![Tarayıcı pencerelerini karşılaştıran ekran görüntüsü](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="f7bca-231">Cloud Shell'de, doğrulanmış/ısınmış evreleme yuvasını üretime dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="f7bca-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="f7bca-232">Takasın iki tarayıcı penceresini yenileyerek gerçekleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f7bca-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Takastan sonra tarayıcı pencerelerini karşılaştırma](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="f7bca-234">Özet</span><span class="sxs-lookup"><span data-stu-id="f7bca-234">Summary</span></span>

<span data-ttu-id="f7bca-235">Bu bölümde, aşağıdaki görevler tamamlandı:</span><span class="sxs-lookup"><span data-stu-id="f7bca-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="f7bca-236">Örnek uygulamayı indirip oluşturup oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f7bca-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="f7bca-237">Azure Bulut Kabuğu'nu kullanarak bir Azure Uygulama Hizmeti Web Uygulaması oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="f7bca-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="f7bca-238">Örnek uygulamayı Git'i kullanarak Azure'a dağıttı.</span><span class="sxs-lookup"><span data-stu-id="f7bca-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="f7bca-239">Visual Studio'yu kullanarak uygulamaya bir değişiklik dağıtıldı.</span><span class="sxs-lookup"><span data-stu-id="f7bca-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="f7bca-240">Web uygulamasına bir evreleme yuvası eklendi.</span><span class="sxs-lookup"><span data-stu-id="f7bca-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="f7bca-241">Evreleme yuvasına bir güncelleştirme dağıtıldı.</span><span class="sxs-lookup"><span data-stu-id="f7bca-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="f7bca-242">Evreleme ve üretim yuvalarını değiştirdim.</span><span class="sxs-lookup"><span data-stu-id="f7bca-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="f7bca-243">Sonraki bölümde, Azure Ardışık Hatları ile DevOps ardışık bir boru hattı oluşturmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="f7bca-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="f7bca-244">Ek okuma</span><span class="sxs-lookup"><span data-stu-id="f7bca-244">Additional reading</span></span>

* [<span data-ttu-id="f7bca-245">Web Apps'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="f7bca-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="f7bca-246">Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="f7bca-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="f7bca-247">Azure Uygulama Hizmeti için dağıtım kimlik bilgilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f7bca-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="f7bca-248">Azure App Service’te hazırlık ortamları ayarlama</span><span class="sxs-lookup"><span data-stu-id="f7bca-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
