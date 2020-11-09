---
title: Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: ASP.NET Core uygulamasının Visual Studio kullanarak Azure App Service nasıl yayımlanacağını öğrenin.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060228"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="a2f98-103">Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="a2f98-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="a2f98-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a2f98-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="a2f98-105">MacOS 'ta çalışıyorsanız [Mac için Visual Studio kullanarak Azure App Service Için Web uygulaması yayımlama](/visualstudio/mac/publish-app-svc?view=vsmac-2019) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="a2f98-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="a2f98-106">App Service dağıtım sorununu gidermek için, bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="a2f98-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="a2f98-107">Kurulum</span><span class="sxs-lookup"><span data-stu-id="a2f98-107">Set up</span></span>

* <span data-ttu-id="a2f98-108">Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.</span><span class="sxs-lookup"><span data-stu-id="a2f98-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="a2f98-109">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a2f98-109">Create a web app</span></span>

<span data-ttu-id="a2f98-110">Visual Studio başlangıç sayfasında **dosya > yeni > proje...** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="a2f98-112">**Yeni proje** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="a2f98-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="a2f98-113">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-113">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="a2f98-114">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-114">Select **Next** .</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="a2f98-116">**Yeni ASP.NET Core Web uygulaması** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="a2f98-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="a2f98-117">**Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-117">Select **Web Application** .</span></span>
* <span data-ttu-id="a2f98-118">Kimlik doğrulaması altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-118">Select **Change** under Authentication.</span></span>

![Yeni ASP.NET Core Web iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="a2f98-120">**Kimlik doğrulamasını Değiştir** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a2f98-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="a2f98-121">**Bireysel kullanıcı hesapları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-121">Select **Individual User Accounts** .</span></span>
* <span data-ttu-id="a2f98-122">**Yeni ASP.NET Core Web uygulamasına** geri dönmek için **Tamam** ' ı seçin ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create** .</span></span>

![Yeni ASP.NET Core Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="a2f98-124">Visual Studio çözümü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a2f98-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="a2f98-125">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a2f98-125">Run the app</span></span>

* <span data-ttu-id="a2f98-126">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="a2f98-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="a2f98-127">**Gizlilik** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-127">Test the **Privacy** link.</span></span>

![Web uygulaması, localhost üzerinde Microsoft Edge 'de açık](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="a2f98-129">Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="a2f98-129">Register a user</span></span>

* <span data-ttu-id="a2f98-130">**Kaydet** ve yeni bir Kullanıcı Kaydet ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="a2f98-131">Kurgusal bir e-posta adresi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a2f98-131">You can use a fictitious email address.</span></span> <span data-ttu-id="a2f98-132">Gönderdiğinizde, sayfada şu hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="a2f98-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="a2f98-133">*"İstek işlenirken bir veritabanı işlemi başarısız oldu. Uygulama DB bağlamı için mevcut geçişleri uygulamak, bu sorunu çözebilir. "*</span><span class="sxs-lookup"><span data-stu-id="a2f98-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="a2f98-134">**Geçişleri Uygula** ' yı seçin ve sayfa güncelleştirildiğinde sayfayı yenileyin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![İstek işlenirken bir veritabanı işlemi başarısız oldu.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="a2f98-137">Uygulama, yeni kullanıcıyı ve bir **oturum kapatma** bağlantısını kaydetmek için kullanılan e-postayı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="a2f98-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Web uygulaması Microsoft Edge 'de açılır.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="a2f98-140">Uygulamayı Azure’da dağıtma</span><span class="sxs-lookup"><span data-stu-id="a2f98-140">Deploy the app to Azure</span></span>

<span data-ttu-id="a2f98-141">Çözüm Gezgini projeye sağ tıklayıp **Yayımla...** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-141">Right-click on the project in Solution Explorer and select **Publish...** .</span></span>

![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="a2f98-143">**Yayımla** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="a2f98-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="a2f98-144">**Azure** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-144">Select **Azure** .</span></span>
* <span data-ttu-id="a2f98-145">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-145">Select **Next** .</span></span>

![Yayımla iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="a2f98-147">**Yayımla** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="a2f98-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="a2f98-148">**Azure App Service (Linux)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-148">Select **Azure App Service (Linux)** .</span></span>
* <span data-ttu-id="a2f98-149">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-149">Select **Next** .</span></span>

![Yayımla Iletişim kutusu: Azure hizmeti seçin](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="a2f98-151">**Yayımla** iletişim kutusunda **yeni Azure App Service oluştur ' u seçin...**</span><span class="sxs-lookup"><span data-stu-id="a2f98-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Yayımla iletişim kutusu: Azure hizmet örneği seçin](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="a2f98-153">**App Service oluştur** iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="a2f98-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="a2f98-154">**Uygulama adı** , **kaynak grubu** ve **App Service planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="a2f98-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="a2f98-155">Bu adları koruyabilir veya değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a2f98-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="a2f98-156">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-156">Select **Create** .</span></span>

![App Service Oluştur iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="a2f98-158">Oluşturma işlemi tamamlandıktan sonra iletişim kutusu otomatik olarak kapatılır ve **Yayımla** iletişim kutusu odağı yeniden alır:</span><span class="sxs-lookup"><span data-stu-id="a2f98-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="a2f98-159">Yeni oluşturulan örnek otomatik olarak seçilir.</span><span class="sxs-lookup"><span data-stu-id="a2f98-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="a2f98-160">**Son** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-160">Select **Finish** .</span></span>

![Yayımla iletişim kutusu: App Service örneği seçin](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="a2f98-162">Daha sonra **profil Yayımlama Özeti** sayfasını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="a2f98-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="a2f98-163">Visual Studio, bu uygulamanın SQL Server bir veritabanı gerektirdiğini algıladı ve bunu yapılandırmanızı istiyor.</span><span class="sxs-lookup"><span data-stu-id="a2f98-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="a2f98-164">**Yapılandır** 'ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-164">Select **Configure** .</span></span>

![Yayımlama profili Özet sayfası: SQL Server bağımlılığı yapılandırma](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="a2f98-166">**Bağımlılığı Yapılandır** iletişim kutusu görünür:</span><span class="sxs-lookup"><span data-stu-id="a2f98-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="a2f98-167">**Azure SQL veritabanı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-167">Select **Azure SQL Database** .</span></span>
* <span data-ttu-id="a2f98-168">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-168">Select **Next** .</span></span>

![SQL Server bağımlılığı iletişim kutusunu Yapılandır](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="a2f98-170">**Azure SQL veritabanı 'Nı Yapılandır** ILETIŞIM kutusunda **SQL veritabanı oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Azure SQL veritabanı 'nı yapılandırma iletişim kutusu](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="a2f98-172">**Azure SQL veritabanı oluştur** görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="a2f98-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="a2f98-173">**Veritabanı adı** , **kaynak grubu** , **veritabanı sunucusu** ve **App Service planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="a2f98-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="a2f98-174">Bu değerleri tutabilir veya değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a2f98-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="a2f98-175">Seçili **veritabanı sunucusu** için **veritabanı yöneticisi Kullanıcı adı** ve **veritabanı yönetici parolasını** girin (kullandığınız hesabın, yeni Azure SQL veritabanı oluşturmak için gerekli izinlere sahip olması gerekir)</span><span class="sxs-lookup"><span data-stu-id="a2f98-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="a2f98-176">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-176">Select **Create** .</span></span>

![Yeni Azure SQL veritabanı iletişim kutusu](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="a2f98-178">Oluşturma işlemi tamamlandıktan sonra iletişim kutusu otomatik olarak kapatılır ve **Azure SQL veritabanı 'Nı Yapılandır** iletişim kutusu odağı yeniden alır:</span><span class="sxs-lookup"><span data-stu-id="a2f98-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="a2f98-179">Yeni oluşturulan örnek otomatik olarak seçilir.</span><span class="sxs-lookup"><span data-stu-id="a2f98-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="a2f98-180">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-180">Select **Next** .</span></span>

![Azure SQL veritabanı 'nı yapılandırma iletişim kutusu](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="a2f98-182">**Azure SQL veritabanı yapılandırma** iletişim kutusunun sonraki adımında:</span><span class="sxs-lookup"><span data-stu-id="a2f98-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="a2f98-183">**Veritabanı bağlantısı Kullanıcı adı** ve **veritabanı bağlantısı parolası** alanlarını girin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="a2f98-184">Bunlar, uygulamanızın çalışma zamanında veritabanına bağlanmak için kullanacağı ayrıntılardır.</span><span class="sxs-lookup"><span data-stu-id="a2f98-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="a2f98-185">En iyi yöntem, önceki adımda kullanılan yönetici Kullanıcı adı & parolasıyla aynı ayrıntıların kullanılmasını önmaktır.</span><span class="sxs-lookup"><span data-stu-id="a2f98-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="a2f98-186">**Son** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-186">Select **Finish** .</span></span>

![Azure SQL veritabanı iletişim kutusunu, bağlantı dizesi ayrıntılarını yapılandırma](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="a2f98-188">**Profil Özeti Yayımla** sayfasında **Ayarlar** ' ı seçin:</span><span class="sxs-lookup"><span data-stu-id="a2f98-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![Yayımlama profili Özet sayfası: Ayarları Düzenle](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="a2f98-190">**Yayımla** Iletişim kutusunun **Ayarlar** sayfasında:</span><span class="sxs-lookup"><span data-stu-id="a2f98-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="a2f98-191">**Veritabanları** ' nı genişletin ve **çalışma zamanında bu bağlantı dizesini kullan** ' ı işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-191">Expand **Databases** and check **Use this connection string at runtime** .</span></span>
* <span data-ttu-id="a2f98-192">**Entity Framework geçişleri** genişletin ve **Bu geçişi yayınla Uygula** ' yı işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish** .</span></span>

* <span data-ttu-id="a2f98-193">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-193">Select **Save** .</span></span> <span data-ttu-id="a2f98-194">Visual Studio **Yayımla** iletişim kutusuna geri döner.</span><span class="sxs-lookup"><span data-stu-id="a2f98-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="a2f98-196">**Yayımla** ’ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a2f98-196">Click **Publish** .</span></span> <span data-ttu-id="a2f98-197">Visual Studio, uygulamanızı Azure 'da yayımlar.</span><span class="sxs-lookup"><span data-stu-id="a2f98-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="a2f98-198">Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.</span><span class="sxs-lookup"><span data-stu-id="a2f98-198">When the deployment completes, the app is opened in a browser.</span></span>

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="a2f98-200">Uygulamayı güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="a2f98-200">Update the app</span></span>

* <span data-ttu-id="a2f98-201">*Pages/Index. cshtml* Razor sayfasını düzenleyin ve içeriğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="a2f98-202">Örneğin, paragrafı "Merhaba ASP.NET Core!" olacak şekilde değiştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a2f98-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="a2f98-203">**Profili Yayımla Özeti** sayfasından **Yayımla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Yayımlama profili Özet sayfası](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="a2f98-205">Uygulama yayımlandıktan sonra, yaptığınız değişikliklerin Azure 'da kullanılabilir olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a2f98-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Görevin tamamlandığını doğrulama](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="a2f98-207">Temizleme</span><span class="sxs-lookup"><span data-stu-id="a2f98-207">Clean up</span></span>

<span data-ttu-id="a2f98-208">Uygulamayı test etmeyi bitirdiğinizde [Azure Portal](https://portal.azure.com/) gidin ve uygulamayı silin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="a2f98-209">**Kaynak grupları** ' nı ve ardından oluşturduğunuz kaynak grubunu seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Azure portalı: kenar çubuğu menüsündeki kaynak grupları](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="a2f98-211">**Kaynak grupları** sayfasında **Sil** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-211">In the **Resource groups** page, select **Delete** .</span></span>

![Azure portalı: kaynak grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="a2f98-213">Kaynak grubunun adını girip **Sil** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a2f98-213">Enter the name of the resource group and select **Delete** .</span></span> <span data-ttu-id="a2f98-214">Uygulamanız ve bu öğreticide oluşturulan diğer tüm kaynaklar artık Azure 'dan silinir.</span><span class="sxs-lookup"><span data-stu-id="a2f98-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="a2f98-215">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="a2f98-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="a2f98-216">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a2f98-216">Additional resources</span></span>

* <span data-ttu-id="a2f98-217">Visual Studio Code için bkz. [Yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="a2f98-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="a2f98-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a2f98-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="a2f98-219">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="a2f98-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="a2f98-220">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="a2f98-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>