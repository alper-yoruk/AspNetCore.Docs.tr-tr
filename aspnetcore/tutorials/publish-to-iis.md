---
title: IIS'de bir ASP.NET Core uygulaması yayınlama
author: rick-anderson
description: Bir ASP.NET Core uygulamasını bir IIS sunucusunda nasıl barındırılacIz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511398"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="79678-103">IIS'de bir ASP.NET Core uygulaması yayınlama</span><span class="sxs-lookup"><span data-stu-id="79678-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="79678-104">Bu öğretici, bir IIS sunucusunda ASP.NET Core uygulamasının nasıl barındırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="79678-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="79678-105">Bu öğretici aşağıdaki konuları kapsar:</span><span class="sxs-lookup"><span data-stu-id="79678-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="79678-106">.NET Core Hosting Paketini Windows Server'a yükleyin.</span><span class="sxs-lookup"><span data-stu-id="79678-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="79678-107">IIS Manager'da bir IIS sitesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="79678-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="79678-108">Bir ASP.NET Core uygulaması dağıtın.</span><span class="sxs-lookup"><span data-stu-id="79678-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="79678-109">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="79678-109">Prerequisites</span></span>

* <span data-ttu-id="79678-110">[.NET Core SDK](/dotnet/core/sdk) geliştirme makinesine yüklendi.</span><span class="sxs-lookup"><span data-stu-id="79678-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="79678-111">Windows **Server, Web Server (IIS)** sunucu rolüyle yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="79678-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="79678-112">Sunucunuz IIS'li web sitelerini barındıracak şekilde yapılandırılmamışsa, <xref:host-and-deploy/iis/index#iis-configuration> makalenin *IIS yapılandırma* bölümündeki kılavuzu izleyin ve ardından bu öğreticiye geri dönün.</span><span class="sxs-lookup"><span data-stu-id="79678-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="79678-113">**IIS yapılandırması ve web sitesi güvenliği, bu öğretici kapsamında olmayan kavramları içerir.**</span><span class="sxs-lookup"><span data-stu-id="79678-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="79678-114">IIS'de üretim uygulamalarını barındırmadan önce [Microsoft IIS belgelerindeki IIS](https://www.iis.net/) kılavuzuna ve [IIS ile barındırma yla ilgili ASP.NET Core makalesine](xref:host-and-deploy/iis/index) başvurun.</span><span class="sxs-lookup"><span data-stu-id="79678-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="79678-115">Bu öğretici kapsamında olmayan IIS barındırma için önemli senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="79678-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="79678-116">ASP.NET Çekirdek Veri Koruması için kayıt defteri kovanının oluşturulması</span><span class="sxs-lookup"><span data-stu-id="79678-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="79678-117">Uygulama havuzunun Erişim Kontrol Listesinin (ACL) yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79678-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="79678-118">IIS dağıtım kavramlarına odaklanmak için bu öğretici, IIS'de yapılandırılan HTTPS güvenliği olmayan bir uygulama dağıtır.</span><span class="sxs-lookup"><span data-stu-id="79678-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="79678-119">HTTPS protokolü için etkin leştirilmiş bir uygulamayı barındırma hakkında daha fazla bilgi için, bu makalenin [Ek kaynaklar](#additional-resources) bölümündeki güvenlik konularına bakın.</span><span class="sxs-lookup"><span data-stu-id="79678-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="79678-120"><xref:host-and-deploy/iis/index> Makalede, ASP.NET Core uygulamaları barındırma küçlükiçin daha fazla kılavuz sunulmuştur.</span><span class="sxs-lookup"><span data-stu-id="79678-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="79678-121">.NET Core Hosting Paketini yükleyin</span><span class="sxs-lookup"><span data-stu-id="79678-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="79678-122">*.NET Core Hosting Paketini* IIS sunucusuna yükleyin.</span><span class="sxs-lookup"><span data-stu-id="79678-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="79678-123">Pakette .NET Core Runtime, .NET Core Library ve [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)yüklenir.</span><span class="sxs-lookup"><span data-stu-id="79678-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79678-124">Modül, ASP.NET Core uygulamalarının IIS'nin arkasında çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="79678-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="79678-125">Aşağıdaki bağlantıyı kullanarak yükleyiciindir:</span><span class="sxs-lookup"><span data-stu-id="79678-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="79678-126">Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)</span><span class="sxs-lookup"><span data-stu-id="79678-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="79678-127">Yükleyicisi IIS sunucusunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="79678-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="79678-128">Sunucuyu yeniden başlatın veya net stop çalıştırın **/y** bir komut kabuğunda **net start w3svc** izledi.</span><span class="sxs-lookup"><span data-stu-id="79678-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="79678-129">IIS sitesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="79678-129">Create the IIS site</span></span>

1. <span data-ttu-id="79678-130">IIS sunucusunda, uygulamanın yayımlanmış klasörlerini ve dosyalarını içerecek bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="79678-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="79678-131">Aşağıdaki adımda, klasörün yolu uygulamanın fiziksel yolu olarak IIS'ye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="79678-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="79678-132">IIS Manager'da, **Bağlantılar** panelinde sunucu düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="79678-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="79678-133">**Siteler** klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="79678-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="79678-134">Bağlamsal menüden **Web Sitesi Ekle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="79678-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="79678-135">Bir **Site adı** sağlayın ve **Fiziksel yolu** oluşturduğunuz uygulamanın dağıtım klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="79678-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="79678-136">**Bağlama** yapılandırmasını sağlayın ve **Tamam'ı**seçerek web sitesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="79678-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="79678-137">ASP.NET Core Razor Pages uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="79678-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="79678-138">Razor <xref:getting-started> Pages uygulaması oluşturmak için öğreticiyi izleyin.</span><span class="sxs-lookup"><span data-stu-id="79678-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="79678-139">Uygulamayı yayımlama ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="79678-139">Publish and deploy the app</span></span>

<span data-ttu-id="79678-140">*Bir uygulama yayımlamak,* bir sunucu tarafından barındırılabilen derlenmiş bir uygulama üretmek anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="79678-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="79678-141">*Bir uygulamayı dağıtmak,* yayınlanan uygulamayı barındırma sistemine taşımak anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="79678-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="79678-142">Yayımlama adımı [.NET Core SDK](/dotnet/core/sdk)tarafından işlenirken, dağıtım adımı çeşitli yaklaşımlarla işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="79678-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="79678-143">Bu öğretici, *klasör* dağıtım yaklaşımını benimser:</span><span class="sxs-lookup"><span data-stu-id="79678-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="79678-144">Uygulama bir klasörde yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="79678-144">The app is published to a folder.</span></span>
* <span data-ttu-id="79678-145">Klasörün içeriği IIS sitesinin klasörüne (IIS Manager'daki siteye **fiziksel yol)** taşınır.</span><span class="sxs-lookup"><span data-stu-id="79678-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="79678-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79678-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="79678-147">**Solution Explorer'da** projeye sağ tıklayın ve **Yayımla'yı**seçin.</span><span class="sxs-lookup"><span data-stu-id="79678-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="79678-148">**Yayımlama hedef** iletişim kutusunda Klasör **yayımlama** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="79678-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="79678-149">Klasör **veya Dosya Paylaşımı** yolunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="79678-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="79678-150">Geliştirme makinesinde ağ paylaşımı olarak kullanılabilen IIS sitesi için bir klasör oluşturduysanız, paylaşıma giden yolu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="79678-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="79678-151">Geçerli kullanıcı nın paylaşıma yayımlamak için yazma erişimine sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="79678-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="79678-152">IIS sunucusundaki IIS site klasörüne doğrudan dağıtamıyorsanız, kaldırılabilir ortamla ilgili bir klasöre yayımlayın ve yayınlanan uygulamayı fiziksel olarak sitenin IIS Yöneticisi'ndeki **Fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="79678-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="79678-153">*Depo gözü/Yayın/{TARGET FRAMEWORK}/publish* klasörünün içeriğini, sitenin IIS Yöneticisi'ndeki Fiziksel **yolu** olan sunucudaki IIS site klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="79678-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="79678-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="79678-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="79678-155">Komut kabuğunda, [uygulamayı dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuyla Serbest Bırak yapılandırmasında yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="79678-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="79678-156">*Depo gözü/Yayın/{TARGET FRAMEWORK}/publish* klasörünün içeriğini, sitenin IIS Yöneticisi'ndeki Fiziksel **yolu** olan sunucudaki IIS site klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="79678-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="79678-157">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79678-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="79678-158">**Çözüm'deki** projeye sağ tıklayın ve**Klasöre Yayımla'yı** **Publish** > seçin.</span><span class="sxs-lookup"><span data-stu-id="79678-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="79678-159">Klasör yolunu **seç'i** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="79678-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="79678-160">Geliştirme makinesinde ağ paylaşımı olarak kullanılabilen IIS sitesi için bir klasör oluşturduysanız, paylaşıma giden yolu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="79678-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="79678-161">Geçerli kullanıcı nın paylaşıma yayımlamak için yazma erişimine sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="79678-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="79678-162">IIS sunucusundaki IIS site klasörüne doğrudan dağıtamıyorsanız, kaldırılabilir ortamla ilgili bir klasöre yayımlayın ve yayınlanan uygulamayı fiziksel olarak sitenin IIS Yöneticisi'ndeki **Fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="79678-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="79678-163">*Depo gözü/Yayın/{TARGET FRAMEWORK}/publish* klasörünün içeriğini, sitenin IIS Yöneticisi'ndeki Fiziksel **yolu** olan sunucudaki IIS site klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="79678-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="79678-164">Web sitesine göz atın</span><span class="sxs-lookup"><span data-stu-id="79678-164">Browse the website</span></span>

<span data-ttu-id="79678-165">Uygulama, ilk isteği aldıktan sonra bir tarayıcıda erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="79678-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="79678-166">Site için IIS Manager'da oluşturduğunuz bitiş noktası bağlama noktasında uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="79678-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="79678-167">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="79678-167">Next steps</span></span>

<span data-ttu-id="79678-168">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="79678-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="79678-169">.NET Core Hosting Paketini Windows Server'a yükleyin.</span><span class="sxs-lookup"><span data-stu-id="79678-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="79678-170">IIS Manager'da bir IIS sitesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="79678-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="79678-171">Bir ASP.NET Core uygulaması dağıtın.</span><span class="sxs-lookup"><span data-stu-id="79678-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="79678-172">IIS'de ASP.NET Core uygulamalarını barındırma hakkında daha fazla bilgi edinmek için IIS Genel Bakış makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="79678-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="79678-173">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="79678-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="79678-174">ASP.NET Çekirdek dokümantasyon setindeki makaleler</span><span class="sxs-lookup"><span data-stu-id="79678-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="79678-175">ASP.NET Core uygulama dağıtımıyla ilgili makaleler</span><span class="sxs-lookup"><span data-stu-id="79678-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="79678-176">Mac için Visual Studio'u kullanarak bir klasöre Web uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="79678-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="79678-177">IIS HTTPS yapılandırması ile ilgili makaleler</span><span class="sxs-lookup"><span data-stu-id="79678-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="79678-178">IIS Yöneticisi'nde SSL yapılandırma</span><span class="sxs-lookup"><span data-stu-id="79678-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="79678-179">IIS'de SSL Nasıl Ayarlanır?</span><span class="sxs-lookup"><span data-stu-id="79678-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="79678-180">IIS ve Windows Server ile ilgili makaleler</span><span class="sxs-lookup"><span data-stu-id="79678-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="79678-181">Resmi Microsoft IIS Sitesi</span><span class="sxs-lookup"><span data-stu-id="79678-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="79678-182">Windows Server teknik içerik kitaplığı</span><span class="sxs-lookup"><span data-stu-id="79678-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
