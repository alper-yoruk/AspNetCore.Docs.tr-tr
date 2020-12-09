---
title: ASP.NET Core ve Azure ile App Service DevOps 'a uygulama dağıtma
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için ilk adımı Azure App Service için bir ASP.NET Core uygulaması dağıtın.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f1c7acba0b7fb7dc07da576b188e580328ff4b89
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901164"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="23fde-103">App Service için uygulama dağıtma</span><span class="sxs-lookup"><span data-stu-id="23fde-103">Deploy an app to App Service</span></span>

<span data-ttu-id="23fde-104">[Azure App Service](/azure/app-service/) Azure 'un web barındırma platformudur.</span><span class="sxs-lookup"><span data-stu-id="23fde-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="23fde-105">Bir Web uygulamasını Azure App Service dağıtmak, el ile veya otomatik bir işlem tarafından yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="23fde-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="23fde-106">Kılavuzun bu bölümü, komut satırını kullanarak el ile veya komut dosyası tarafından tetiklenebilecek veya Visual Studio kullanarak el ile tetiklenen dağıtım yöntemlerini açıklar.</span><span class="sxs-lookup"><span data-stu-id="23fde-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="23fde-107">Bu bölümde, aşağıdaki görevleri yerine getirirsiniz:</span><span class="sxs-lookup"><span data-stu-id="23fde-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="23fde-108">Örnek uygulamayı indirin ve derleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-108">Download and build the sample app.</span></span>
* <span data-ttu-id="23fde-109">Azure Cloud Shell kullanarak Azure App Service bir Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="23fde-110">Git kullanarak örnek uygulamayı Azure 'a dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23fde-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="23fde-111">Visual Studio 'Yu kullanarak uygulamaya bir değişiklik dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23fde-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="23fde-112">Web uygulamasına bir hazırlama yuvası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="23fde-113">Hazırlama yuvasına bir güncelleştirme dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23fde-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="23fde-114">Hazırlama ve üretim yuvalarını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23fde-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="23fde-115">Uygulamayı indirme ve test etme</span><span class="sxs-lookup"><span data-stu-id="23fde-115">Download and test the app</span></span>

<span data-ttu-id="23fde-116">Bu kılavuzda kullanılan uygulama, önceden oluşturulmuş bir ASP.NET Core uygulaması, [basit bir akış okuyucusu](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="23fde-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="23fde-117">Bu, bir Razor `Microsoft.SyndicationFeed.ReaderWriter` RSS/Atom akışı almak ve haber öğelerini bir listede göstermek için API kullanan bir sayfalar uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="23fde-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="23fde-118">Kodu gözden geçirmeniz ücretsizdir, ancak bu uygulamayla ilgili özel bir şey olmadığını anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="23fde-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="23fde-119">Bu, tanım amaçları için yalnızca basit bir ASP.NET Core uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="23fde-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="23fde-120">Bir komut kabuğundan kodu indirin, projeyi derleyin ve aşağıdaki gibi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="23fde-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="23fde-121">*Note: Linux/macOS kullanıcıları, ters eğik çizgi () yerine eğik çizgi () kullanarak yollar için uygun değişiklikleri yapmalıdır `/` `\` .*</span><span class="sxs-lookup"><span data-stu-id="23fde-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="23fde-122">Kodu yerel makinenizde bir klasöre kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="23fde-123">Çalışma klasörünüzü, oluşturulan *basit akış okuyucusu* klasörü ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23fde-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="23fde-124">Paketleri geri yükleyin ve çözümü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="23fde-125">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="23fde-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![DotNet Run komutu başarılı](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="23fde-127">Bir tarayıcıyı açın ve `http://localhost:5000` dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="23fde-128">Uygulama, bir dağıtım akışı URL 'SI yazabilir veya yapıştırmanıza ve haber öğelerinin bir listesini görüntülemenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="23fde-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![RSS akışı içeriğini görüntüleyen uygulama](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="23fde-130">Uygulamanın doğru şekilde çalışmaya başladıktan sonra **Ctrl** + komut kabuğu 'nda CTRL **C** tuşuna basarak kapatın.</span><span class="sxs-lookup"><span data-stu-id="23fde-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="23fde-131">Azure App Service Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="23fde-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="23fde-132">Uygulamayı dağıtmak için bir App Service [Web uygulaması](/azure/app-service/app-service-web-overview)oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="23fde-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="23fde-133">Web uygulaması oluşturulduktan sonra Git kullanarak yerel makinenizden buna dağıtırsınız.</span><span class="sxs-lookup"><span data-stu-id="23fde-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="23fde-134">[Azure Cloud Shell](https://shell.azure.com/bash)'de oturum açın.</span><span class="sxs-lookup"><span data-stu-id="23fde-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="23fde-135">Note: ilk kez oturum açtığınızda, Cloud Shell yapılandırma dosyaları için bir depolama hesabı oluşturmak isteyip istemediğinizi sorar.</span><span class="sxs-lookup"><span data-stu-id="23fde-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="23fde-136">Varsayılanları kabul edin veya benzersiz bir ad sağlayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="23fde-137">Aşağıdaki adımlar için Cloud Shell kullanın.</span><span class="sxs-lookup"><span data-stu-id="23fde-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="23fde-138">a.</span><span class="sxs-lookup"><span data-stu-id="23fde-138">a.</span></span> <span data-ttu-id="23fde-139">Web uygulamanızın adını depolamak için bir değişken bildirin.</span><span class="sxs-lookup"><span data-stu-id="23fde-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="23fde-140">Ad, varsayılan URL 'de kullanılmak üzere benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="23fde-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="23fde-141">`$RANDOM`Adı oluşturmak için bash işlevinin kullanılması, benzersizliği garanti eder ve biçimde sonuçlanır `webappname99999` .</span><span class="sxs-lookup"><span data-stu-id="23fde-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="23fde-142">b.</span><span class="sxs-lookup"><span data-stu-id="23fde-142">b.</span></span> <span data-ttu-id="23fde-143">Bir kaynak grubu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-143">Create a resource group.</span></span> <span data-ttu-id="23fde-144">Kaynak grupları, Azure kaynaklarını bir grup olarak yönetilecek şekilde toplamanız için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="23fde-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="23fde-145">`az`Komut, [Azure CLI](/cli/azure/)'yı çağırır.</span><span class="sxs-lookup"><span data-stu-id="23fde-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="23fde-146">CLı yerel olarak çalıştırılabilir, ancak Cloud Shell kullanılması zaman ve yapılandırmayı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="23fde-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="23fde-147">c.</span><span class="sxs-lookup"><span data-stu-id="23fde-147">c.</span></span> <span data-ttu-id="23fde-148">S1 katmanında bir App Service planı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="23fde-149">App Service planı, aynı fiyatlandırma katmanını paylaşan Web Apps 'in bir gruplandırmasıdır.</span><span class="sxs-lookup"><span data-stu-id="23fde-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="23fde-150">S1 katmanı ücretsizdir, ancak hazırlama yuvaları özelliği için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="23fde-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="23fde-151">d.</span><span class="sxs-lookup"><span data-stu-id="23fde-151">d.</span></span> <span data-ttu-id="23fde-152">Aynı kaynak grubundaki App Service planını kullanarak Web uygulaması kaynağını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="23fde-153">e.</span><span class="sxs-lookup"><span data-stu-id="23fde-153">e.</span></span> <span data-ttu-id="23fde-154">Dağıtım kimlik bilgilerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-154">Set the deployment credentials.</span></span> <span data-ttu-id="23fde-155">Bu dağıtım kimlik bilgileri, aboneliğinizdeki tüm Web uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="23fde-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="23fde-156">Kullanıcı adında özel karakterler kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="23fde-157">f.</span><span class="sxs-lookup"><span data-stu-id="23fde-157">f.</span></span> <span data-ttu-id="23fde-158">Web uygulamasını yerel git 'ten dağıtımları kabul edecek şekilde yapılandırın ve *Git DAĞıTıM URL*'sini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="23fde-159">**Başvuru için bu URL 'yi daha sonra dikkate alın**.</span><span class="sxs-lookup"><span data-stu-id="23fde-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="23fde-160">örneğin:</span><span class="sxs-lookup"><span data-stu-id="23fde-160">g.</span></span> <span data-ttu-id="23fde-161">*Web uygulaması URL 'sini* görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-161">Display the *web app URL*.</span></span> <span data-ttu-id="23fde-162">Boş Web uygulamasını görmek için bu URL 'ye gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="23fde-163">**Başvuru için bu URL 'yi daha sonra dikkate alın**.</span><span class="sxs-lookup"><span data-stu-id="23fde-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="23fde-164">Yerel makinenizde bir komut kabuğu kullanarak, Web uygulamasının proje klasörüne (örneğin, `.\simple-feed-reader\SimpleFeedReader` ) gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="23fde-165">Dağıtım URL 'sine göndermek için git 'i ayarlamak üzere aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="23fde-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="23fde-166">a.</span><span class="sxs-lookup"><span data-stu-id="23fde-166">a.</span></span> <span data-ttu-id="23fde-167">Uzak URL 'YI yerel depoya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="23fde-168">b.</span><span class="sxs-lookup"><span data-stu-id="23fde-168">b.</span></span> <span data-ttu-id="23fde-169">Yerel varsayılan dalı (*ana*) *Azure-prod* uzak öğesinin varsayılan dalına (*ana*) gönderin.</span><span class="sxs-lookup"><span data-stu-id="23fde-169">Push the local default branch (*master*) to the *azure-prod* remote's default branch (*master*).</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="23fde-170">Daha önce oluşturduğunuz dağıtım kimlik bilgileri istenir.</span><span class="sxs-lookup"><span data-stu-id="23fde-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="23fde-171">Komut kabuğu 'ndaki çıktıyı gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-171">Observe the output in the command shell.</span></span> <span data-ttu-id="23fde-172">Azure, ASP.NET Core uygulamasını uzaktan oluşturur.</span><span class="sxs-lookup"><span data-stu-id="23fde-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="23fde-173">Bir tarayıcıda, *Web UYGULAMASı URL* 'sine gidin ve uygulamanın oluşturulup dağıtıldığını aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="23fde-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="23fde-174">İle yerel git deposuna ek değişiklikler uygulanabilir `git commit` .</span><span class="sxs-lookup"><span data-stu-id="23fde-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="23fde-175">Bu değişiklikler, önceki komutla Azure 'a gönderilir `git push` .</span><span class="sxs-lookup"><span data-stu-id="23fde-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="23fde-176">Visual Studio ile dağıtım</span><span class="sxs-lookup"><span data-stu-id="23fde-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="23fde-177">*Note: Bu bölüm yalnızca Windows için geçerlidir. Linux ve macOS kullanıcıları aşağıdaki adım 2 ' de açıklanan değişikliği yapması gerekir. Dosyayı kaydedin ve yerel depoya ile değişikliği yürütün `git commit` . Son olarak, değişikliği `git push` ilk bölümde olduğu gibi, ile gönderin.*</span><span class="sxs-lookup"><span data-stu-id="23fde-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="23fde-178">Uygulama, komut kabuğundan zaten dağıtıldı.</span><span class="sxs-lookup"><span data-stu-id="23fde-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="23fde-179">Uygulamaya bir güncelleştirme dağıtmak için Visual Studio 'nun tümleşik araçlarını kullanalım.</span><span class="sxs-lookup"><span data-stu-id="23fde-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="23fde-180">Visual Studio, arka planda komut satırı araçlarıyla aynı şeyi gerçekleştirir, ancak Visual Studio 'nun tanıdık kullanıcı arabirimi içinde.</span><span class="sxs-lookup"><span data-stu-id="23fde-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="23fde-181">Visual Studio 'da *Simplefeedreader. sln* ' i açın.</span><span class="sxs-lookup"><span data-stu-id="23fde-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="23fde-182">Çözüm Gezgini, *Pages\ındex.cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="23fde-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="23fde-183">`<h2>Simple Feed Reader</h2>`Olarak değiştirin `<h2>Simple Feed Reader - V2</h2>` .</span><span class="sxs-lookup"><span data-stu-id="23fde-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="23fde-184">**Ctrl** + **Shift** + Uygulamayı derlemek için CTRL SHIFT **B** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="23fde-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="23fde-185">Çözüm Gezgini, projeye sağ tıklayın ve **Yayımla**' ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Sağ tıklama, yayımlamayı gösteren ekran görüntüsü](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="23fde-187">Visual Studio yeni bir App Service kaynağı oluşturabilir, ancak bu güncelleştirme mevcut dağıtım üzerinden yayımlanacak.</span><span class="sxs-lookup"><span data-stu-id="23fde-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="23fde-188">**Bir yayımlama hedefi seç** iletişim kutusunda, sol taraftaki listeden **App Service** ' yi seçin ve ardından **Varolanı Seç**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="23fde-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="23fde-189">**Yayımla**’ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-189">Click **Publish**.</span></span>
6. <span data-ttu-id="23fde-190">**App Service** iletişim kutusunda, Azure aboneliğinizi oluşturmak Için kullanılan Microsoft veya kuruluş hesabının sağ üst köşede görüntülendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="23fde-191">Aksi takdirde, açılan eklentiye tıklayın ve ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="23fde-192">Doğru Azure **aboneliğinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="23fde-193">**Görünüm** Için **kaynak grubu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="23fde-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="23fde-194">**AzureTutorial** kaynak grubunu genişletin ve ardından mevcut Web uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="23fde-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="23fde-195">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-195">Click **OK**.</span></span>

    ![Yayımla App Service iletişim kutusunu gösteren ekran görüntüsü](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="23fde-197">Visual Studio, uygulamayı oluşturur ve Azure 'a dağıtır.</span><span class="sxs-lookup"><span data-stu-id="23fde-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="23fde-198">Web uygulaması URL 'sine gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-198">Browse to the web app URL.</span></span> <span data-ttu-id="23fde-199">`<h2>`Öğe değişikliğini canlı olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-199">Validate that the `<h2>` element modification is live.</span></span>

![Değiştirilen başlığa sahip uygulama](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="23fde-201">Dağıtım yuvaları</span><span class="sxs-lookup"><span data-stu-id="23fde-201">Deployment slots</span></span>

<span data-ttu-id="23fde-202">Dağıtım yuvaları, üretimde çalışan uygulamayı etkilemeden değişikliklerin hazırlanmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="23fde-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="23fde-203">Uygulamanın hazırlanmış sürümü bir kalite güvencesi ekibi tarafından doğrulandıktan sonra, üretim ve hazırlama yuvaları takas edilebilir.</span><span class="sxs-lookup"><span data-stu-id="23fde-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="23fde-204">Hazırlama aşamasındaki uygulama üretime bu şekilde yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="23fde-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="23fde-205">Aşağıdaki adımlar bir hazırlama yuvası oluşturur, bu üzerinde bazı değişiklikler dağıtır ve doğrulama işleminden sonra hazırlama yuvasını üretim ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="23fde-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="23fde-206">Henüz oturum açmadıysanız [Azure Cloud Shell](https://shell.azure.com/bash)oturum açın.</span><span class="sxs-lookup"><span data-stu-id="23fde-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="23fde-207">Hazırlama yuvasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-207">Create the staging slot.</span></span>

    <span data-ttu-id="23fde-208">a.</span><span class="sxs-lookup"><span data-stu-id="23fde-208">a.</span></span> <span data-ttu-id="23fde-209">*Hazırlama* adına sahip bir dağıtım yuvası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23fde-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="23fde-210">b.</span><span class="sxs-lookup"><span data-stu-id="23fde-210">b.</span></span> <span data-ttu-id="23fde-211">Hazırlama yuvasını yerel git 'ten dağıtımı kullanacak şekilde yapılandırın ve **hazırlama** dağıtımı URL 'sini alın.</span><span class="sxs-lookup"><span data-stu-id="23fde-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="23fde-212">**Başvuru için bu URL 'yi daha sonra dikkate alın**.</span><span class="sxs-lookup"><span data-stu-id="23fde-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="23fde-213">c.</span><span class="sxs-lookup"><span data-stu-id="23fde-213">c.</span></span> <span data-ttu-id="23fde-214">Hazırlama yuvasının URL 'sini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-214">Display the staging slot's URL.</span></span> <span data-ttu-id="23fde-215">Boş hazırlama yuvasını görmek için URL 'ye gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="23fde-216">**Başvuru için bu URL 'yi daha sonra dikkate alın**.</span><span class="sxs-lookup"><span data-stu-id="23fde-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="23fde-217">Bir metin düzenleyicisinde veya Visual Studio 'da, *sayfa/Index. cshtml* öğesini tekrar değiştirerek `<h2>` öğenin `<h2>Simple Feed Reader - V3</h2>` dosyayı okuyup kaydetmesini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="23fde-218">Dosyayı, Visual Studio 'nun *Takım Gezgini* sekmesindeki **değişiklikler** sayfasını kullanarak veya yerel makinenin komut kabuğunu kullanarak aşağıdakini girerek Yerel git deposuna kaydedin:</span><span class="sxs-lookup"><span data-stu-id="23fde-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="23fde-219">Yerel makinenin komut kabuğunu kullanarak, hazırlama dağıtımı URL 'sini git uzak olarak ekleyin ve kaydedilen değişiklikleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="23fde-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="23fde-220">a.</span><span class="sxs-lookup"><span data-stu-id="23fde-220">a.</span></span> <span data-ttu-id="23fde-221">Hazırlama için uzak URL 'YI yerel git deposuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="23fde-222">b.</span><span class="sxs-lookup"><span data-stu-id="23fde-222">b.</span></span> <span data-ttu-id="23fde-223">Yerel varsayılan dalı (*ana*) *Azure hazırlama* uzak öğesinin varsayılan dalına (*ana*) gönderin.</span><span class="sxs-lookup"><span data-stu-id="23fde-223">Push the local default branch (*master*) to the *azure-staging* remote's default branch (*master*).</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="23fde-224">Azure uygulamayı oluşturup dağıttığında bekleyin.</span><span class="sxs-lookup"><span data-stu-id="23fde-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="23fde-225">V3 'in hazırlama yuvasına dağıtıldığını doğrulamak için iki tarayıcı penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="23fde-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="23fde-226">Bir pencerede, özgün Web uygulaması URL 'sine gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="23fde-227">Diğer pencerede, hazırlama Web uygulaması URL 'sine gidin.</span><span class="sxs-lookup"><span data-stu-id="23fde-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="23fde-228">Üretim URL 'si uygulamanın v2 'ye hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="23fde-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="23fde-229">Hazırlama URL 'SI uygulamanın V3 öğesine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="23fde-229">The staging URL serves V3 of the app.</span></span>

    ![Tarayıcı pencerelerini karşılaştıran ekran görüntüsü](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="23fde-231">Cloud Shell, doğrulanan/warmed hazırlama yuvasını üretim olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23fde-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="23fde-232">İki tarayıcı pencerelerini yenileyerek değiştirme gerçekleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23fde-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Değiştirme sonrasında tarayıcı pencerelerini karşılaştırma](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="23fde-234">Özet</span><span class="sxs-lookup"><span data-stu-id="23fde-234">Summary</span></span>

<span data-ttu-id="23fde-235">Bu bölümde, aşağıdaki görevler tamamlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="23fde-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="23fde-236">Örnek uygulama indirildi ve oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="23fde-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="23fde-237">Azure Cloud Shell kullanarak bir Azure App Service Web uygulaması oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="23fde-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="23fde-238">Git kullanarak örnek uygulamayı Azure 'a dağıttınız.</span><span class="sxs-lookup"><span data-stu-id="23fde-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="23fde-239">Visual Studio kullanılarak uygulamaya bir değişiklik dağıtıldı.</span><span class="sxs-lookup"><span data-stu-id="23fde-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="23fde-240">Web uygulamasına bir hazırlama yuvası eklendi.</span><span class="sxs-lookup"><span data-stu-id="23fde-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="23fde-241">Hazırlama yuvasına bir güncelleştirme dağıtıldı.</span><span class="sxs-lookup"><span data-stu-id="23fde-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="23fde-242">Hazırlama ve üretim yuvaları takas edilen.</span><span class="sxs-lookup"><span data-stu-id="23fde-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="23fde-243">Sonraki bölümde Azure Pipelines bir DevOps işlem hattı oluşturmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="23fde-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="23fde-244">Ek okuma</span><span class="sxs-lookup"><span data-stu-id="23fde-244">Additional reading</span></span>

* [<span data-ttu-id="23fde-245">Web Apps genel bakış</span><span class="sxs-lookup"><span data-stu-id="23fde-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="23fde-246">Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="23fde-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="23fde-247">Azure App Service için dağıtım kimlik bilgilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="23fde-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="23fde-248">Azure App Service’ta hazırlık ortamları ayarlama</span><span class="sxs-lookup"><span data-stu-id="23fde-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
