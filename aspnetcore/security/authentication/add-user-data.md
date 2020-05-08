---
title: ASP.NET Core projesindeki Kullanıcı verilerini Identity ekleme, indirme ve silme
author: rick-anderson
description: ASP.NET Core projesindeki özel kullanıcı verilerinin Identity nasıl ekleneceğini öğrenin. GDPR başına verileri silme.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777338"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="e6aeb-104">ASP.NET Core projesindeki özel kullanıcı verilerini kimliğe ekleme, indirme ve silme</span><span class="sxs-lookup"><span data-stu-id="e6aeb-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="e6aeb-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6aeb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e6aeb-106">Bu makalede nasıl yapılacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-106">This article shows how to:</span></span>

* <span data-ttu-id="e6aeb-107">ASP.NET Core Web uygulamasına özel kullanıcı verileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="e6aeb-108">Özel Kullanıcı veri modelini <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> özniteliği ile işaretleyin, bu nedenle otomatik olarak indirilebilir ve silinemez.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="e6aeb-109">Verilerin indirilip silinebilmesini sağlamak, [GDPR](xref:security/gdpr) gereksinimlerini karşılamaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="e6aeb-110">Proje örneği bir Razor Pages Web uygulamasından oluşturulur, ancak yönergeler bir ASP.NET Core MVC web uygulaması için benzerdir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="e6aeb-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6aeb-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6aeb-112">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="e6aeb-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="e6aeb-113">Razor Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="e6aeb-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6aeb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6aeb-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e6aeb-115">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="e6aeb-116">[İndirme örnek](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kodunun ad alanıyla eşleştirmek Istiyorsanız projeyi **WebApp1** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="e6aeb-117">**ASP.NET Core Web uygulaması** > **Tamam ' ı** seçin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="e6aeb-118">Açılan listede **ASP.NET Core 3,0** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="e6aeb-119">**Web uygulaması** > **Tamam 'ı** seçin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="e6aeb-120">Projeyi derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="e6aeb-121">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="e6aeb-122">[İndirme örnek](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kodunun ad alanıyla eşleştirmek Istiyorsanız projeyi **WebApp1** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="e6aeb-123">**ASP.NET Core Web uygulaması** > **Tamam ' ı** seçin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="e6aeb-124">Açılan listede **ASP.NET Core 2,2** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="e6aeb-125">**Web uygulaması** > **Tamam 'ı** seçin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="e6aeb-126">Projeyi derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="e6aeb-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e6aeb-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="e6aeb-128">Identity desteği 'ı çalıştırın</span><span class="sxs-lookup"><span data-stu-id="e6aeb-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6aeb-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6aeb-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6aeb-130">**Çözüm Gezgini**, projeye sağ tıklayıp**Yeni iskli öğe** **Ekle** > >.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e6aeb-131">**Yapı iskelesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik** > **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="e6aeb-132">**Kimlik Ekle** iletişim kutusunda aşağıdaki seçenekler:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="e6aeb-133">Var olan düzen dosyasını seçin *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e6aeb-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="e6aeb-134">Geçersiz kılmak için aşağıdaki dosyaları seçin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-134">Select the following files to override:</span></span>
    * <span data-ttu-id="e6aeb-135">**Hesap/kayıt**</span><span class="sxs-lookup"><span data-stu-id="e6aeb-135">**Account/Register**</span></span>
    * <span data-ttu-id="e6aeb-136">**Hesap/yönet/Dizin**</span><span class="sxs-lookup"><span data-stu-id="e6aeb-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="e6aeb-137">Yeni bir **+** **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="e6aeb-138">Proje **WebApp1**olarak adlandırılmışsa türü (**WebApp1. modeller. WebApp1Context** ) kabul edin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="e6aeb-139">Yeni bir **+** **Kullanıcı sınıfı**oluşturmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="e6aeb-140">**Ekle**> ( **projenin adı\*\*\*\*WebApp1User** ) öğesini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="e6aeb-141">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e6aeb-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e6aeb-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e6aeb-143">ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e6aeb-144">Proje (. csproj) dosyasına [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="e6aeb-145">Proje dizininde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="e6aeb-146">Identity desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="e6aeb-147">Proje klasöründe, kimlik scaffolder ' ı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="e6aeb-148">Aşağıdaki adımları gerçekleştirmek için [geçişler, UseAuthentication ve düzen](xref:security/authentication/scaffold-identity#efm) bölümündeki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="e6aeb-149">Bir geçiş oluşturun ve veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="e6aeb-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="e6aeb-151">Düzen `<partial name="_LoginPartial" />` dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="e6aeb-152">Uygulamayı test etme:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-152">Test the app:</span></span>
  * <span data-ttu-id="e6aeb-153">Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="e6aeb-153">Register a user</span></span>
  * <span data-ttu-id="e6aeb-154">Yeni Kullanıcı adını ( **oturum kapatma** bağlantısının yanında) seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="e6aeb-155">Kullanıcı adını ve diğer bağlantıları göstermek için pencereyi genişletmeniz veya gezinti çubuğu simgesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="e6aeb-156">**Kişisel veri** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="e6aeb-157">**İndir** düğmesini seçin ve *persondata. JSON* dosyasını inceledi.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="e6aeb-158">Oturum açan kullanıcıyı silen **Sil** düğmesini test edin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="e6aeb-159">Identity DB 'ye özel kullanıcı verileri ekleme</span><span class="sxs-lookup"><span data-stu-id="e6aeb-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="e6aeb-160">`IdentityUser` Türetilmiş sınıfı özel özelliklerle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="e6aeb-161">Projeyi WebApp1 olarak adlandırdıysanız, dosya *Areas/Identity/Data/WebApp1User. cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="e6aeb-162">Dosyayı aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="e6aeb-163">[Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) özniteliğiyle birlikte bulunan özellikler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="e6aeb-164">*Areas/Identity/Pages/Account/Manage/Deletepersonal Data. cshtml* Razor sayfası çağrılarından `UserManager.Delete`sonra silinir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="e6aeb-165">*Alan/kimlik/sayfa/hesap/Yönet/Downloadpersonal Data. cshtml* Razor sayfasında indirilen verilere dahildir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="e6aeb-166">Hesap/yönet/Index. cshtml sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="e6aeb-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="e6aeb-167">`InputModel` *Bölgeleri/kimliği/sayfaları/hesabı/Yönet/Index. cshtml. cs* ' de aşağıdaki vurgulanmış kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="e6aeb-168">*Bölgeleri/kimliği/sayfaları/hesabı/Yönet/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="e6aeb-169">*Bölgeleri/kimliği/sayfaları/hesabı/Yönet/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="e6aeb-170">Account/Register. cshtml sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="e6aeb-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="e6aeb-171">`InputModel` *Areas/Identity/Pages/Account/Register. cshtml. cs* dosyasında aşağıdaki vurgulanmış kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="e6aeb-172">*Areas/Identity/Pages/Account/Register. cshtml* ' i aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="e6aeb-173">*Areas/Identity/Pages/Account/Register. cshtml* ' i aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="e6aeb-174">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="e6aeb-175">Özel Kullanıcı verileri için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="e6aeb-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6aeb-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6aeb-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6aeb-177">Visual Studio **Paket Yöneticisi konsolunda**:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="e6aeb-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e6aeb-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="e6aeb-179">Test oluşturma, görüntüleme, indirme, özel kullanıcı verilerini silme</span><span class="sxs-lookup"><span data-stu-id="e6aeb-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="e6aeb-180">Uygulamayı test etme:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-180">Test the app:</span></span>

* <span data-ttu-id="e6aeb-181">Yeni bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-181">Register a new user.</span></span>
* <span data-ttu-id="e6aeb-182">`/Identity/Account/Manage` Sayfadaki özel kullanıcı verilerini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="e6aeb-183">Kullanıcıların kişisel verilerini `/Identity/Account/Manage/PersonalData` sayfadan indirip görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="e6aeb-184">IUserClaimsPrincipalFactory Identity kullanarak talepler ekleme<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="e6aeb-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="e6aeb-185">`IUserClaimsPrincipalFactory<T>` Arabirimi kullanılarak ASP.NET Core Identity ek talepler eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="e6aeb-186">Bu sınıf, `Startup.ConfigureServices` yöntemi içinde uygulamaya eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e6aeb-187">Sınıfının özel uygulamasını şu şekilde ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e6aeb-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="e6aeb-188">Demo kodu `ApplicationUser` sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="e6aeb-189">Bu sınıf, ek `IsAdmin` talep eklemek için kullanılan bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="e6aeb-190">, `AdditionalUserClaimsPrincipalFactory` `UserClaimsPrincipalFactory` Arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="e6aeb-191">Yeni bir rol talebi öğesine eklenir `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="e6aeb-192">Ek talep daha sonra uygulamada kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="e6aeb-193">Bir Razor sayfada, `IAuthorizationService` örnek, talep değerine erişmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6aeb-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
