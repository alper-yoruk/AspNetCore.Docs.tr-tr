---
title: IIS 'de ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: ASP.NET Core uygulamasının bir IIS sunucusunda nasıl barındırılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-iis
ms.openlocfilehash: aa79ce604539b4f09d6f17d4f43da28a6b615f53
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774580"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="d7f98-103">IIS 'de ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="d7f98-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="d7f98-104">Bu öğreticide bir IIS sunucusunda ASP.NET Core uygulamasının nasıl barındırılacağını gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d7f98-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="d7f98-105">Bu öğreticide aşağıdaki konular ele alınmaktadır:</span><span class="sxs-lookup"><span data-stu-id="d7f98-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7f98-106">.NET Core barındırma paketi 'ni Windows Server 'a yükler.</span><span class="sxs-lookup"><span data-stu-id="d7f98-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="d7f98-107">IIS Yöneticisi 'nde bir IIS sitesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7f98-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="d7f98-108">ASP.NET Core uygulamasını dağıtın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7f98-109">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d7f98-109">Prerequisites</span></span>

* <span data-ttu-id="d7f98-110">Geliştirme makinesinde yüklü [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="d7f98-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="d7f98-111">**Web sunucusu (IIS)** sunucu rolüyle yapılandırılmış Windows Server.</span><span class="sxs-lookup"><span data-stu-id="d7f98-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="d7f98-112">Sunucunuz Web sitelerini IIS ile barındırmak üzere yapılandırılmamışsa, makalenin *IIS yapılandırması* bölümündeki yönergeleri izleyin <xref:host-and-deploy/iis/index#iis-configuration> ve ardından Bu öğreticiye geri dönün.</span><span class="sxs-lookup"><span data-stu-id="d7f98-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="d7f98-113">**IIS yapılandırması ve Web sitesi güvenliği, bu öğretici kapsamında olmayan kavramları içerir.**</span><span class="sxs-lookup"><span data-stu-id="d7f98-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="d7f98-114">IIS 'de üretim uygulamalarını barındırmadan önce, [MICROSOFT IIS BELGELERINDEKI](https://www.iis.net/) IIS KıLAVUZUNA ve [IIS ile barındırma hakkında ASP.NET Core makalesine](xref:host-and-deploy/iis/index) başvurun.</span><span class="sxs-lookup"><span data-stu-id="d7f98-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="d7f98-115">Bu öğreticide kapsanmayan IIS barındırması için önemli senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d7f98-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="d7f98-116">ASP.NET Core veri koruması için bir kayıt defteri kovanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7f98-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="d7f98-117">Uygulama havuzunun Access Control listesi (ACL) yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d7f98-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="d7f98-118">IIS dağıtım kavramlarına odaklanmak için bu öğretici, IIS 'de HTTPS güvenliği olmayan bir uygulama dağıtır.</span><span class="sxs-lookup"><span data-stu-id="d7f98-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="d7f98-119">HTTPS protokolü için etkinleştirilmiş bir uygulamayı barındırma hakkında daha fazla bilgi için, bu makalenin [ek kaynaklar](#additional-resources) bölümündeki güvenlik konularına bakın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="d7f98-120">ASP.NET Core uygulamalar barındırılmasına yönelik daha fazla rehberlik makalesinde sunulmaktadır <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="d7f98-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="d7f98-121">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="d7f98-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d7f98-122">*.NET Core barındırma paketi* 'ni IIS sunucusuna yükler.</span><span class="sxs-lookup"><span data-stu-id="d7f98-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="d7f98-123">Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="d7f98-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d7f98-124">Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d7f98-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="d7f98-125">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="d7f98-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="d7f98-126">Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="d7f98-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="d7f98-127">Yükleyiciyi IIS sunucusunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="d7f98-128">Sunucuyu yeniden başlatın ya da **net stop idi** ' i ve ardından bir komut kabuğu 'ndan **net start w3svc** ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="d7f98-129">IIS sitesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7f98-129">Create the IIS site</span></span>

1. <span data-ttu-id="d7f98-130">IIS sunucusunda, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7f98-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="d7f98-131">Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d7f98-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="d7f98-132">IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="d7f98-133">**Siteler** klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="d7f98-134">Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="d7f98-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="d7f98-135">Bir **site adı** belirtin ve **fiziksel yolu** , oluşturduğunuz uygulamanın dağıtım klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="d7f98-136">**Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7f98-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="d7f98-137">ASP.NET Core Razor Pages uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7f98-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="d7f98-138"><xref:getting-started>Razor Pages uygulaması oluşturmak için öğreticiyi izleyin.</span><span class="sxs-lookup"><span data-stu-id="d7f98-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="d7f98-139">Uygulamayı yayımlama ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="d7f98-139">Publish and deploy the app</span></span>

<span data-ttu-id="d7f98-140">*Uygulama yayımlama* , bir sunucu tarafından barındırılabilecek derlenmiş bir uygulama oluşturmak anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d7f98-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="d7f98-141">*Uygulama dağıtma* , yayımlanan uygulamayı bir barındırma sistemine taşımak anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d7f98-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="d7f98-142">Yayımla adımı [.NET Core SDK](/dotnet/core/sdk)tarafından işlenir, ancak dağıtım adımı çeşitli yaklaşımlar tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="d7f98-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="d7f98-143">Bu öğretici, şu konumda *klasör* dağıtım yaklaşımını benimsemektedir:</span><span class="sxs-lookup"><span data-stu-id="d7f98-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="d7f98-144">Uygulama bir klasöre yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="d7f98-144">The app is published to a folder.</span></span>
* <span data-ttu-id="d7f98-145">Klasörün içeriği IIS sitesinin klasörüne taşınır (IIS Yöneticisi 'nde sitenin **fiziksel yolu** ).</span><span class="sxs-lookup"><span data-stu-id="d7f98-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d7f98-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7f98-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d7f98-147">**Çözüm Gezgini** projede projeye sağ tıklayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="d7f98-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="d7f98-148">**Bir yayımlama hedefi seç** iletişim kutusunda, **klasörü** Yayımla seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="d7f98-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="d7f98-149">**Klasör veya dosya paylaşma** yolunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="d7f98-150">Geliştirme makinesinde bir ağ paylaşımında bulunan IIS sitesi için bir klasör oluşturduysanız, paylaşımın yolunu belirtin.</span><span class="sxs-lookup"><span data-stu-id="d7f98-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="d7f98-151">Geçerli kullanıcının paylaşıma yayımlamak için yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d7f98-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="d7f98-152">IIS sunucusunda IIS site klasörüne doğrudan dağıtım yapadıysanız, kaldırılabilir medyada bir klasöre yayımlayın ve yayımlanan uygulamayı sunucuda, sitenin **fiziksel yolu** olan sunucudaki IIS site klasörüne fiziksel olarak taşıyın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="d7f98-153">*Bin/Release/{Target Framework}/Publish* klasörünün IÇERIĞINI sunucusundaki IIS site klasörüne taşıyın, bu site, sitenin IIS Yöneticisi 'ndeki **fiziksel yoludur** .</span><span class="sxs-lookup"><span data-stu-id="d7f98-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d7f98-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7f98-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="d7f98-155">Bir komut kabuğunda, uygulamayı sürüm yapılandırması 'nda [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuyla yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="d7f98-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="d7f98-156">*Bin/Release/{Target Framework}/Publish* klasörünün IÇERIĞINI sunucusundaki IIS site klasörüne taşıyın, bu site, sitenin IIS Yöneticisi 'ndeki **fiziksel yoludur** .</span><span class="sxs-lookup"><span data-stu-id="d7f98-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d7f98-157">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7f98-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d7f98-158">**Çözümdeki** projeye sağ tıklayın ve Yayımla klasörünü **Yayımla ' yı seçin**  >  **Publish to Folder**.</span><span class="sxs-lookup"><span data-stu-id="d7f98-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="d7f98-159">**Klasör seçin** yolunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="d7f98-160">Geliştirme makinesinde bir ağ paylaşımında bulunan IIS sitesi için bir klasör oluşturduysanız, paylaşımın yolunu belirtin.</span><span class="sxs-lookup"><span data-stu-id="d7f98-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="d7f98-161">Geçerli kullanıcının paylaşıma yayımlamak için yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d7f98-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="d7f98-162">IIS sunucusunda IIS site klasörüne doğrudan dağıtım yapadıysanız, kaldırılabilir medyada bir klasöre yayımlayın ve yayımlanan uygulamayı sunucuda, sitenin **fiziksel yolu** olan sunucudaki IIS site klasörüne fiziksel olarak taşıyın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="d7f98-163">*Bin/Release/{Target Framework}/Publish* klasörünün IÇERIĞINI sunucusundaki IIS site klasörüne taşıyın, bu site, sitenin IIS Yöneticisi 'ndeki **fiziksel yoludur** .</span><span class="sxs-lookup"><span data-stu-id="d7f98-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="d7f98-164">Web sitesine gidin</span><span class="sxs-lookup"><span data-stu-id="d7f98-164">Browse the website</span></span>

<span data-ttu-id="d7f98-165">Uygulamanın ilk isteği aldıktan sonra tarayıcıda erişilebilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d7f98-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="d7f98-166">Site için IIS Yöneticisi 'nde oluşturduğunuz uç nokta bağlamasındaki uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7f98-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7f98-167">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="d7f98-167">Next steps</span></span>

<span data-ttu-id="d7f98-168">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="d7f98-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7f98-169">.NET Core barındırma paketi 'ni Windows Server 'a yükler.</span><span class="sxs-lookup"><span data-stu-id="d7f98-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="d7f98-170">IIS Yöneticisi 'nde bir IIS sitesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7f98-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="d7f98-171">ASP.NET Core uygulamasını dağıtın.</span><span class="sxs-lookup"><span data-stu-id="d7f98-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="d7f98-172">IIS 'de ASP.NET Core uygulamaları barındırma hakkında daha fazla bilgi için bkz. IIS genel bakış makalesi:</span><span class="sxs-lookup"><span data-stu-id="d7f98-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="d7f98-173">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d7f98-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="d7f98-174">ASP.NET Core belge kümesi makaleleri</span><span class="sxs-lookup"><span data-stu-id="d7f98-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="d7f98-175">ASP.NET Core Uygulama dağıtımıyla ilgili makaleler</span><span class="sxs-lookup"><span data-stu-id="d7f98-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="d7f98-176">Mac için Visual Studio kullanarak bir Web uygulamasını bir klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="d7f98-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="d7f98-177">IIS HTTPS yapılandırması makaleleri</span><span class="sxs-lookup"><span data-stu-id="d7f98-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="d7f98-178">IIS Yöneticisi 'nde SSL 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d7f98-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="d7f98-179">IIS 'de SSL ayarlama</span><span class="sxs-lookup"><span data-stu-id="d7f98-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="d7f98-180">IIS ve Windows Server makaleleri</span><span class="sxs-lookup"><span data-stu-id="d7f98-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="d7f98-181">Resmi Microsoft IIS sitesi</span><span class="sxs-lookup"><span data-stu-id="d7f98-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="d7f98-182">Windows Server teknik içerik kitaplığı</span><span class="sxs-lookup"><span data-stu-id="d7f98-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
