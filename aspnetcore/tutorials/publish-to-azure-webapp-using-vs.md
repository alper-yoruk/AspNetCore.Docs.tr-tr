---
title: Visual Studio ile Azure'da ASP.NET Core uygulaması yayınlama
author: rick-anderson
description: Visual Studio'ASP.NET bir ASP.NET Core uygulamasını Azure Uygulama Hizmeti'nde nasıl yayınlayacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662206"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="40ea0-103">Visual Studio ile Azure'da ASP.NET Core uygulaması yayınlama</span><span class="sxs-lookup"><span data-stu-id="40ea0-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="40ea0-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="40ea0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="40ea0-105">Bkz. macOS üzerinde çalışıyorsanız [Mac için Visual Studio'u kullanarak Azure Uygulama Hizmeti'ne web uygulaması yayımla.](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)</span><span class="sxs-lookup"><span data-stu-id="40ea0-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="40ea0-106">Bir Uygulama Hizmeti dağıtım sorununu <xref:test/troubleshoot-azure-iis>gidermek için bkz.</span><span class="sxs-lookup"><span data-stu-id="40ea0-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="40ea0-107">Ayarla</span><span class="sxs-lookup"><span data-stu-id="40ea0-107">Set up</span></span>

* <span data-ttu-id="40ea0-108">Hesabınız yoksa ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.</span><span class="sxs-lookup"><span data-stu-id="40ea0-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="40ea0-109">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="40ea0-109">Create a web app</span></span>

<span data-ttu-id="40ea0-110">Visual Studio Başlangıç Sayfasında **Dosya > Yeni > Projesi'ni seçin...**</span><span class="sxs-lookup"><span data-stu-id="40ea0-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="40ea0-112">Yeni **Proje** iletişim kutusunu tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="40ea0-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="40ea0-113">Sol bölmede **.NET Core'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="40ea0-114">Orta **bölmede, Core Web Uygulaması ASP.NET**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="40ea0-115">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-115">Select **OK**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="40ea0-117">Yeni **ASP.NET Çekirdek Web Uygulaması** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="40ea0-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="40ea0-118">**Web Uygulamasını**Seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-118">Select **Web Application**.</span></span>
* <span data-ttu-id="40ea0-119">**Kimlik Doğrulamasını Değiştir'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-119">Select **Change Authentication**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="40ea0-121">**Kimlik Doğrulamasını Değiştir** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="40ea0-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="40ea0-122">**Bireysel Kullanıcı Hesapları**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="40ea0-123">Yeni ASP.NET Çekirdek **Web Uygulamasına**dönmek için **Tamam'ı** seçin, ardından tekrar **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Yeni ASP.NET Core Web kimlik doğrulama iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="40ea0-125">Visual Studio çözümü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40ea0-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="40ea0-126">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="40ea0-126">Run the app</span></span>

* <span data-ttu-id="40ea0-127">Projeyi çalıştırmak için CTRL+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="40ea0-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="40ea0-128">**Hakkında** ve **İletişim** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-128">Test the **About** and **Contact** links.</span></span>

![Web uygulaması localhost microsoft edge açık](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="40ea0-130">Bir kullanıcıyı kaydetme</span><span class="sxs-lookup"><span data-stu-id="40ea0-130">Register a user</span></span>

* <span data-ttu-id="40ea0-131">**Kaydol'u** seçin ve yeni bir kullanıcı kaydettirin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="40ea0-132">Hayali bir e-posta adresi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40ea0-132">You can use a fictitious email address.</span></span> <span data-ttu-id="40ea0-133">Gönderdiğiniz zaman, sayfa aşağıdaki hatayı görüntüler:</span><span class="sxs-lookup"><span data-stu-id="40ea0-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="40ea0-134">*"İç Sunucu Hatası: Bir veritabanı işlemi isteği işlerken başarısız oldu. SQL özel durumu: Veritabanını açamıyor. Uygulama DB bağlamı için varolan geçişleri uygulamak bu sorunu çözebilir."*</span><span class="sxs-lookup"><span data-stu-id="40ea0-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="40ea0-135">**Geçişuygula'yı** seçin ve sayfa güncellendikten sonra sayfayı yenileyin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![İç Sunucu Hatası: Bir veritabanı işlemi isteği işlerken başarısız oldu.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="40ea0-139">Uygulama, yeni kullanıcıyı kaydetmek için kullanılan e-postayı ve **Oturum Dışı** bağlantısını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="40ea0-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Web uygulaması Microsoft Edge'de açılır.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="40ea0-142">Uygulamayı Azure’da dağıtma</span><span class="sxs-lookup"><span data-stu-id="40ea0-142">Deploy the app to Azure</span></span>

<span data-ttu-id="40ea0-143">Solution Explorer'da projeye sağ tıklayın ve **Yayımla'yı seçin...**.</span><span class="sxs-lookup"><span data-stu-id="40ea0-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Bağlamsal menü açık ve Publish bağlantısı vurgulanır](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="40ea0-145">**Yayımla** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="40ea0-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="40ea0-146">**Microsoft Azure Uygulama Hizmeti'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="40ea0-147">Vites simgesini seçin ve ardından **Profil Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="40ea0-148">**Profil Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-148">Select **Create Profile**.</span></span>

![İletişim kutusu yayımlama](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="40ea0-150">Azure kaynakları oluşturma</span><span class="sxs-lookup"><span data-stu-id="40ea0-150">Create Azure resources</span></span>

<span data-ttu-id="40ea0-151">**Uygulama Hizmeti Oluştur** iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="40ea0-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="40ea0-152">Aboneliğinizi girin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-152">Enter your subscription.</span></span>
* <span data-ttu-id="40ea0-153">**Uygulama Adı,** **Kaynak Grubu**ve Uygulama **Hizmet Planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="40ea0-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="40ea0-154">Bu adları saklayabilir veya değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40ea0-154">You can keep these names or change them.</span></span>

![Uygulama Hizmeti iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="40ea0-156">Yeni bir veritabanı oluşturmak için **Hizmetler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="40ea0-157">Yeni bir **+** SQL Veritabanı oluşturmak için yeşil simgeyi seçin</span><span class="sxs-lookup"><span data-stu-id="40ea0-157">Select the green **+** icon to create a new SQL Database</span></span>

![Yeni SQL Veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="40ea0-159">Yeni bir veritabanı oluşturmak için **SQL Veritabanını Yapılandır'da** **Yeni...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Yeni SQL Veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="40ea0-161">**SQL Server'ı Yapılandır** ı iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="40ea0-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="40ea0-162">Yönetici kullanıcı adı ve parola girin ve ardından **Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="40ea0-163">Varsayılan **Sunucu Adını**tutabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40ea0-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="40ea0-164">Yönetici kullanıcı adı olarak "yönetici"ye izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="40ea0-164">"admin" isn't allowed as the administrator user name.</span></span>

![SQL Server iletişim kutusunu yapılandırma](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="40ea0-166">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-166">Select **OK**.</span></span>

<span data-ttu-id="40ea0-167">Visual **Studio, Uygulama Hizmeti Oluştur** iletişim kutusuna geri döner.</span><span class="sxs-lookup"><span data-stu-id="40ea0-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="40ea0-168">Uygulama Hizmeti **Oluştur** iletişim kutusunda **Oluştur'u** seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-168">Select **Create** on the **Create App Service** dialog.</span></span>

![SQL Veritabanı iletişim kutusunu yapılandırma](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="40ea0-170">Visual Studio, Azure'da Web uygulamasını ve SQL Server'ı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40ea0-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="40ea0-171">Bu adım birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="40ea0-171">This step can take a few minutes.</span></span> <span data-ttu-id="40ea0-172">Oluşturulan kaynaklar hakkında bilgi için [ek kaynaklara](#additional-resources)bakın.</span><span class="sxs-lookup"><span data-stu-id="40ea0-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="40ea0-173">Dağıtım tamamlandığında **Ayarlar'ı**seçin:</span><span class="sxs-lookup"><span data-stu-id="40ea0-173">When deployment completes, select **Settings**:</span></span>

![SQL Server iletişim kutusunu yapılandırma](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="40ea0-175">**Yayımla** iletişim kutusunun **Ayarlar** sayfasında:</span><span class="sxs-lookup"><span data-stu-id="40ea0-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="40ea0-176">**Veritabanlarını** genişletin ve **çalışma zamanında bu bağlantı dizelerini kullanın**denetleyin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="40ea0-177">**Varlık Çerçeve Geçişlerini** Genişletin ve bu geçişi yayımlamaya uygulayın' ı **denetleyin.**</span><span class="sxs-lookup"><span data-stu-id="40ea0-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="40ea0-178">**Kaydet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-178">Select **Save**.</span></span> <span data-ttu-id="40ea0-179">Visual Studio **Yayımla** iletişim kutusuna döner.</span><span class="sxs-lookup"><span data-stu-id="40ea0-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Yayımlama iletişim kutusu: Ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="40ea0-181">**Yayımla**’ta tıklayın.</span><span class="sxs-lookup"><span data-stu-id="40ea0-181">Click **Publish**.</span></span> <span data-ttu-id="40ea0-182">Visual Studio uygulamanızı Azure'da yayınlar.</span><span class="sxs-lookup"><span data-stu-id="40ea0-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="40ea0-183">Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.</span><span class="sxs-lookup"><span data-stu-id="40ea0-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="40ea0-184">Uygulamanızı Azure’da test edin</span><span class="sxs-lookup"><span data-stu-id="40ea0-184">Test your app in Azure</span></span>

* <span data-ttu-id="40ea0-185">**Hakkında** ve **İletişim** bağlantılarını test edin</span><span class="sxs-lookup"><span data-stu-id="40ea0-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="40ea0-186">Yeni bir kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="40ea0-186">Register a new user</span></span>

![Azure Uygulama Hizmetinde Microsoft Edge'de web uygulaması açıldı](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="40ea0-188">Uygulamayı güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="40ea0-188">Update the app</span></span>

* <span data-ttu-id="40ea0-189">*Sayfaları/About.cshtml* Razor sayfasını edin ve içeriğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="40ea0-190">Örneğin, paragrafı "Hello ASP.NET Core!" diyecek şekilde değiştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="40ea0-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="40ea0-191">Projeye sağ tıklayın ve **Tekrar Yayımla...'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-191">Right-click on the project and select **Publish...** again.</span></span>

![Bağlamsal menü açık ve Publish bağlantısı vurgulanır](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="40ea0-193">Uygulama yayınlandıktan sonra yaptığınız değişikliklerin Azure'da kullanılabildiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="40ea0-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Görevin tamamolduğunu doğrula](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="40ea0-195">Temizleme</span><span class="sxs-lookup"><span data-stu-id="40ea0-195">Clean up</span></span>

<span data-ttu-id="40ea0-196">Uygulamayı test etmeyi bitirdikten sonra [Azure portalına](https://portal.azure.com/) gidin ve uygulamayı silin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="40ea0-197">**Kaynak gruplarını**seçin, ardından oluşturduğunuz kaynak grubunu seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portalı: Kenar çubuğu menüsünde Kaynak Grupları](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="40ea0-199">Kaynak **grupları** sayfasında **Sil'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portalı: Kaynak Grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="40ea0-201">Kaynak grubunun adını girin ve **Sil'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="40ea0-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="40ea0-202">Uygulamanız ve bu eğitimde oluşturulan diğer tüm kaynaklar artık Azure'dan silinir.</span><span class="sxs-lookup"><span data-stu-id="40ea0-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="40ea0-203">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="40ea0-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="40ea0-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="40ea0-204">Additional resources</span></span>

* <span data-ttu-id="40ea0-205">Visual Studio Code için [bkz.](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)</span><span class="sxs-lookup"><span data-stu-id="40ea0-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="40ea0-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="40ea0-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="40ea0-207">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="40ea0-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="40ea0-208">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="40ea0-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
