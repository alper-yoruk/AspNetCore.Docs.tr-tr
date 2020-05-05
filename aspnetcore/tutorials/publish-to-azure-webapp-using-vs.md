---
title: Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: ASP.NET Core uygulamasının Visual Studio kullanarak Azure App Service nasıl yayımlanacağını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777091"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="6f0cb-103">Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="6f0cb-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="6f0cb-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f0cb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="6f0cb-105">MacOS 'ta çalışıyorsanız [Mac için Visual Studio kullanarak Azure App Service Için Web uygulaması yayımlama](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="6f0cb-106">App Service dağıtım sorununu gidermek için, bkz <xref:test/troubleshoot-azure-iis>..</span><span class="sxs-lookup"><span data-stu-id="6f0cb-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="6f0cb-107">Ayarlama</span><span class="sxs-lookup"><span data-stu-id="6f0cb-107">Set up</span></span>

* <span data-ttu-id="6f0cb-108">Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="6f0cb-109">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6f0cb-109">Create a web app</span></span>

<span data-ttu-id="6f0cb-110">Visual Studio başlangıç sayfasında **dosya > yeni > proje...** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="6f0cb-112">**Yeni proje** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="6f0cb-113">Sol bölmede **.NET Core**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="6f0cb-114">Orta bölmede **ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="6f0cb-115">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-115">Select **OK**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="6f0cb-117">**Yeni ASP.NET Core Web uygulaması** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="6f0cb-118">**Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-118">Select **Web Application**.</span></span>
* <span data-ttu-id="6f0cb-119">**Kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-119">Select **Change Authentication**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="6f0cb-121">**Kimlik doğrulamasını Değiştir** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="6f0cb-122">**Bireysel kullanıcı hesapları**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="6f0cb-123">**Yeni ASP.NET Core Web uygulamasına**geri dönmek için **Tamam** ' ı seçin ve ardından yeniden **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Yeni ASP.NET Core Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="6f0cb-125">Visual Studio çözümü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6f0cb-126">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="6f0cb-126">Run the app</span></span>

* <span data-ttu-id="6f0cb-127">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="6f0cb-128">**Hakkında** ve **iletişim** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-128">Test the **About** and **Contact** links.</span></span>

![Web uygulaması, localhost üzerinde Microsoft Edge 'de açık](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="6f0cb-130">Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="6f0cb-130">Register a user</span></span>

* <span data-ttu-id="6f0cb-131">**Kaydet** ve yeni bir Kullanıcı Kaydet ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="6f0cb-132">Kurgusal bir e-posta adresi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-132">You can use a fictitious email address.</span></span> <span data-ttu-id="6f0cb-133">Gönderdiğinizde, sayfada şu hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="6f0cb-134">*"İç sunucu hatası: istek işlenirken bir veritabanı işlemi başarısız oldu. SQL özel durumu: veritabanı açılamıyor. Uygulama DB bağlamı için mevcut geçişleri uygulamak, bu sorunu çözebilir. "*</span><span class="sxs-lookup"><span data-stu-id="6f0cb-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="6f0cb-135">**Geçişleri Uygula** ' yı seçin ve sayfa güncelleştirildiğinde sayfayı yenileyin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![İç sunucu hatası: istek işlenirken bir veritabanı işlemi başarısız oldu.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="6f0cb-139">Uygulama, Yeni Kullanıcı ve bir **Oturum çıkış** bağlantısı kaydetmek için kullanılan e-postayı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Web uygulaması Microsoft Edge 'de açılır.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="6f0cb-142">Uygulamayı Azure’da dağıtma</span><span class="sxs-lookup"><span data-stu-id="6f0cb-142">Deploy the app to Azure</span></span>

<span data-ttu-id="6f0cb-143">Çözüm Gezgini projeye sağ tıklayıp **Yayımla...**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="6f0cb-145">**Yayımla** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="6f0cb-146">**Microsoft Azure App Service**seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="6f0cb-147">Dişli simgesini seçin ve ardından **Profil oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="6f0cb-148">**Profil Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-148">Select **Create Profile**.</span></span>

![Yayımla iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="6f0cb-150">Azure kaynakları oluşturma</span><span class="sxs-lookup"><span data-stu-id="6f0cb-150">Create Azure resources</span></span>

<span data-ttu-id="6f0cb-151">**App Service oluştur** iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="6f0cb-152">Aboneliğinizi girin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-152">Enter your subscription.</span></span>
* <span data-ttu-id="6f0cb-153">**Uygulama adı**, **kaynak grubu**ve **App Service planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="6f0cb-154">Bu adları koruyabilir veya değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-154">You can keep these names or change them.</span></span>

![App Service iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="6f0cb-156">Yeni bir veritabanı oluşturmak için **Hizmetler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="6f0cb-157">Yeni bir SQL **+** veritabanı oluşturmak için yeşil simgesini seçin</span><span class="sxs-lookup"><span data-stu-id="6f0cb-157">Select the green **+** icon to create a new SQL Database</span></span>

![Yeni SQL Veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="6f0cb-159">Yeni bir veritabanı oluşturmak için **SQL veritabanını Yapılandır** Iletişim kutusunda **yeni...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Yeni SQL veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="6f0cb-161">**SQL Server Yapılandır** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="6f0cb-162">Bir Yönetici Kullanıcı adı ve parola girin ve **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="6f0cb-163">Varsayılan **sunucu adını**koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="6f0cb-164">Yönetici Kullanıcı adı olarak "admin" kullanımına izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-164">"admin" isn't allowed as the administrator user name.</span></span>

![SQL Server iletişim kutusunu Yapılandır](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="6f0cb-166">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-166">Select **OK**.</span></span>

<span data-ttu-id="6f0cb-167">Visual Studio, **oluşturma App Service** iletişim kutusuna geri döner.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="6f0cb-168">Oluştur **App Service** Iletişim kutusunda **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-168">Select **Create** on the **Create App Service** dialog.</span></span>

![SQL veritabanını Yapılandır iletişim kutusu](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="6f0cb-170">Visual Studio, Azure üzerinde Web uygulaması ve SQL Server oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="6f0cb-171">Bu adım birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-171">This step can take a few minutes.</span></span> <span data-ttu-id="6f0cb-172">Oluşturulan kaynaklar hakkında daha fazla bilgi için bkz. [ek kaynaklar](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="6f0cb-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="6f0cb-173">Dağıtım tamamlandığında **Ayarlar**' ı seçin:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-173">When deployment completes, select **Settings**:</span></span>

![SQL Server iletişim kutusunu Yapılandır](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="6f0cb-175">**Yayımla** Iletişim kutusunun **Ayarlar** sayfasında:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="6f0cb-176">**Veritabanları** ' nı genişletin ve **çalışma zamanında bu bağlantı dizesini kullan**' ı işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="6f0cb-177">**Entity Framework geçişleri** genişletin ve **Bu geçişi yayınla Uygula**' yı işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="6f0cb-178">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-178">Select **Save**.</span></span> <span data-ttu-id="6f0cb-179">Visual Studio **Yayımla** iletişim kutusuna geri döner.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="6f0cb-181">**Yayımla**’ta tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-181">Click **Publish**.</span></span> <span data-ttu-id="6f0cb-182">Visual Studio, uygulamanızı Azure 'da yayımlar.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="6f0cb-183">Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="6f0cb-184">Uygulamanızı Azure’da test edin</span><span class="sxs-lookup"><span data-stu-id="6f0cb-184">Test your app in Azure</span></span>

* <span data-ttu-id="6f0cb-185">**Hakkında** ve **iletişim** bağlantılarını test edin</span><span class="sxs-lookup"><span data-stu-id="6f0cb-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="6f0cb-186">Yeni bir Kullanıcı Kaydet</span><span class="sxs-lookup"><span data-stu-id="6f0cb-186">Register a new user</span></span>

![Azure App Service üzerinde Microsoft Edge 'de açılan Web uygulaması](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="6f0cb-188">Uygulamayı güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6f0cb-188">Update the app</span></span>

* <span data-ttu-id="6f0cb-189">*Pages/about. cshtml* Razor sayfasını düzenleyin ve içeriğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="6f0cb-190">Örneğin, paragrafı "Merhaba ASP.NET Core!" olacak şekilde değiştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="6f0cb-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="6f0cb-191">Projeye sağ tıklayın ve **Yayımla...** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-191">Right-click on the project and select **Publish...** again.</span></span>

![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="6f0cb-193">Uygulama yayımlandıktan sonra, yaptığınız değişikliklerin Azure 'da kullanılabilir olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Görevin tamamlandığını doğrulama](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="6f0cb-195">Temizleme</span><span class="sxs-lookup"><span data-stu-id="6f0cb-195">Clean up</span></span>

<span data-ttu-id="6f0cb-196">Uygulamayı test etmeyi bitirdiğinizde [Azure Portal](https://portal.azure.com/) gidin ve uygulamayı silin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="6f0cb-197">**Kaynak grupları**' nı ve ardından oluşturduğunuz kaynak grubunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure portalı: kenar çubuğu menüsündeki kaynak grupları](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="6f0cb-199">**Kaynak grupları** sayfasında **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure portalı: kaynak grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="6f0cb-201">Kaynak grubunun adını girip **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="6f0cb-202">Uygulamanız ve bu öğreticide oluşturulan diğer tüm kaynaklar artık Azure 'dan silinir.</span><span class="sxs-lookup"><span data-stu-id="6f0cb-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="6f0cb-203">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="6f0cb-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="6f0cb-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6f0cb-204">Additional resources</span></span>

* <span data-ttu-id="6f0cb-205">Visual Studio Code için bkz. [Yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="6f0cb-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="6f0cb-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6f0cb-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="6f0cb-207">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="6f0cb-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="6f0cb-208">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="6f0cb-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
