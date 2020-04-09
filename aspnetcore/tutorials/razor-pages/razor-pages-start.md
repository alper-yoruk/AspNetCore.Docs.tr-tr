---
title: 'Öğretici: ASP.NET Core Razor Pages ile başlayın'
author: rick-anderson
description: Öğreticiler Bu dizi nasıl ASP.NET Core Razor Pages nasıl kullanılacağını gösterir. Nasıl bir model oluşturacağınızı, Razor sayfaları için kod oluşturmayı, veri erişimi için Entity Framework Core ve SQL Server'ı kullanmayı, arama işlevselliği eklemeyi, giriş doğrulamayı eklemeyi ve modeli güncelleştirmek için geçişleri nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658545"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="1a6aa-104">Öğretici: ASP.NET Core Razor Pages ile başlayın</span><span class="sxs-lookup"><span data-stu-id="1a6aa-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1a6aa-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1a6aa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1a6aa-106">Bu bir ASP.NET Core Razor Pages web uygulaması oluşturma temellerini öğretir bir serinin ilk öğretici.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1a6aa-107">Serinin sonunda, film veritabanını yöneten bir uygulamanız olur.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1a6aa-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1a6aa-109">Razor Pages web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1a6aa-110">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-110">Run the app.</span></span>
> * <span data-ttu-id="1a6aa-111">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-111">Examine the project files.</span></span>

<span data-ttu-id="1a6aa-112">Bu eğitimin sonunda, daha sonraki eğitimlerde üzerine oluşturacağınız çalışan bir Razor Pages web uygulamasına sahip olacaksınız.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1a6aa-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1a6aa-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a6aa-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1a6aa-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1a6aa-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1a6aa-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1a6aa-118">Razor Sayfaları web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="1a6aa-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a6aa-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1a6aa-120">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1a6aa-121">Yeni bir ASP.NET Core Web Uygulaması oluşturun ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1a6aa-122">![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1a6aa-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1a6aa-123">Projeye **RazorPagesMovie**adını verebedin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1a6aa-124">*ProjerazorPagesMovie* adını önemlidir, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1a6aa-125">![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1a6aa-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1a6aa-126">Açılır ASP.NET **Core 3.1'i** seçin, **Web Uygulaması**ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1a6aa-128">Aşağıdaki başlangıç projesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-128">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1a6aa-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1a6aa-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1a6aa-131">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1a6aa-132">Projeyi içerecek dizine`cd`() değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1a6aa-133">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1a6aa-134">Komut, `dotnet new` *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1a6aa-135">Komut, `code` Visual Studio Code'un geçerli örneğinde *RazorPagesMovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1a6aa-136">Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve 'RazorPagesMovie'den hata ayıklama eksiktir. Onları eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="1a6aa-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1a6aa-137">**Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-137">Select **Yes**.</span></span>

  <span data-ttu-id="1a6aa-138">*Launch.json* ve *tasks.json* dosyalarını içeren *bir .vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1a6aa-139">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1a6aa-140">**Dosya** > **Yeni Çözüm**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-140">Select **File** > **New Solution**.</span></span>

![macOS Yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1a6aa-142">**Gelecek** **.NET Core** > **App** > **Web Uygulamasını** > Seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="1a6aa-144">Yeni Web Uygulaması iletişim **günlüğünüze Yapıla,** **Hedef Çerçeveyi** **.NET Core 3.1**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 seçimi](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1a6aa-146">Projeye **RazorPagesMovie**adını ver ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1a6aa-148">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="1a6aa-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="1a6aa-149">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="1a6aa-149">Examine the project files</span></span>

<span data-ttu-id="1a6aa-150">Aşağıda, daha sonraki eğitimlerde çalışacağınız ana proje klasörleri ve dosyalarına genel bir bakış verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1a6aa-151">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="1a6aa-151">Pages folder</span></span>

<span data-ttu-id="1a6aa-152">Razor sayfaları ve destekleyici dosyalar içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1a6aa-153">Her Razor sayfası bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1a6aa-154">Razor sözdizimini kullanarak C# koduyla HTML biçimlendirmesi içeren *bir .cshtml* dosyası.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1a6aa-155">Sayfa olaylarını işleyen C# kodu içeren *.cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1a6aa-156">Destekleyen dosyaların alt altı yla başlayan adları vardır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1a6aa-157">Örneğin, *_Layout.cshtml* dosyası tüm sayfalarda yaygın olan UI öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1a6aa-158">Bu dosya, sayfanın üst kısmındaki gezinti menüsünü ve sayfanın altındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1a6aa-159">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1a6aa-160">wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="1a6aa-160">wwwroot folder</span></span>

<span data-ttu-id="1a6aa-161">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyalar içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1a6aa-162">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1a6aa-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1a6aa-163">appSettings.json</span></span>

<span data-ttu-id="1a6aa-164">Bağlantı dizeleri gibi yapılandırma verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1a6aa-165">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1a6aa-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1a6aa-166">Program.cs</span></span>

<span data-ttu-id="1a6aa-167">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-167">Contains the entry point for the program.</span></span> <span data-ttu-id="1a6aa-168">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1a6aa-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1a6aa-169">Startup.cs</span></span>

<span data-ttu-id="1a6aa-170">Uygulama davranışını yapılandıran kod içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="1a6aa-171">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1a6aa-172">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1a6aa-172">Next steps</span></span>

<span data-ttu-id="1a6aa-173">Serinin bir sonraki öğretici için advance:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1a6aa-174">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="1a6aa-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a6aa-175">Bu bir serinin ilk öğretici.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="1a6aa-176">[Dizi](xref:tutorials/razor-pages/index) bir ASP.NET Core Razor Pages web uygulaması oluşturma temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1a6aa-177">Serinin sonunda, film veritabanını yöneten bir uygulamanız olur.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1a6aa-178">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1a6aa-179">Razor Pages web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1a6aa-180">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-180">Run the app.</span></span>
> * <span data-ttu-id="1a6aa-181">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-181">Examine the project files.</span></span>

<span data-ttu-id="1a6aa-182">Bu eğitimin sonunda, daha sonraki eğitimlerde üzerine oluşturacağınız çalışan bir Razor Pages web uygulamasına sahip olacaksınız.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1a6aa-184">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1a6aa-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a6aa-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1a6aa-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1a6aa-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1a6aa-187">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1a6aa-188">Razor Sayfaları web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="1a6aa-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a6aa-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1a6aa-190">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1a6aa-191">Yeni bir ASP.NET Core Web Uygulaması oluşturun ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1a6aa-193">Projeye **RazorPagesMovie**adını verebedin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1a6aa-194">*ProjerazorPagesMovie* adını önemlidir, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/config.png)

* <span data-ttu-id="1a6aa-196">Açılır ASP.NET **Core 2.2'yi** seçin, **Web Uygulaması**ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1a6aa-198">Aşağıdaki başlangıç projesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-198">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1a6aa-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1a6aa-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1a6aa-201">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1a6aa-202">Projeyi içerecek dizine`cd`() değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1a6aa-203">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1a6aa-204">Komut, `dotnet new` *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1a6aa-205">Komut, `code` Visual Studio Code'un geçerli örneğinde *RazorPagesMovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1a6aa-206">Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve 'RazorPagesMovie'den hata ayıklama eksiktir. Onları eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="1a6aa-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1a6aa-207">**Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-207">Select **Yes**.</span></span>

  <span data-ttu-id="1a6aa-208">*Launch.json* ve *tasks.json* dosyalarını içeren *bir .vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1a6aa-209">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1a6aa-210">**Dosya** > **Yeni Çözüm**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-210">Select **File** > **New Solution**.</span></span>

![macOS Yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1a6aa-212">**Gelecek** **.NET Core** > **App** > **Web Uygulamasını** > Seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="1a6aa-214">Yeni **ASP.NET Core Web API iletişim günlüğünüzde** **Hedef Çerçeveyi** **.NET Core 3.1**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 seçimi](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1a6aa-216">Projeye **RazorPagesMovie**adını ver ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1a6aa-218">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="1a6aa-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a6aa-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1a6aa-220">Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1a6aa-221">Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1a6aa-222">Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="1a6aa-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1a6aa-223">Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1a6aa-224">Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1a6aa-225">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1a6aa-226">Uygulamanın ana sayfasında, izlemeyi kabul etmek için **Kabul** et'i seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1a6aa-227">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa [Birliği'nin Genel Veri Koruma Yönetmeliği'ne (GDPR)](xref:security/gdpr)uymanız gerektiğinde onay özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1a6aa-229">Aşağıdaki resim, izleme izni verdikten sonra uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="1a6aa-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1a6aa-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1a6aa-232">Hata ayıklama olmadan çalıştırmak için **Ctrl-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1a6aa-233">Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `http://localhost:5001`başlattı ve gezinir .</span><span class="sxs-lookup"><span data-stu-id="1a6aa-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1a6aa-234">Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="1a6aa-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1a6aa-235">Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1a6aa-236">Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1a6aa-237">Uygulamanın ana sayfasında, izlemeyi kabul etmek için **Kabul** et'i seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1a6aa-238">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa [Birliği'nin Genel Veri Koruma Yönetmeliği'ne (GDPR)](xref:security/gdpr)uymanız gerektiğinde onay özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1a6aa-240">Aşağıdaki resim, izleme izni verdikten sonra uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1a6aa-242">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a6aa-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1a6aa-243">Hata ayıklama olmadan çalıştırmak için **Cmd-Opt-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1a6aa-244">Visual Studio [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı başlattı `http://localhost:5001`ve gezinir .</span><span class="sxs-lookup"><span data-stu-id="1a6aa-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1a6aa-245">Uygulamanın ana sayfasında, izlemeyi kabul etmek için **Kabul** et'i seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1a6aa-246">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa [Birliği'nin Genel Veri Koruma Yönetmeliği'ne (GDPR)](xref:security/gdpr)uymanız gerektiğinde onay özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1a6aa-248">Aşağıdaki resim, izleme izni verdikten sonra uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1a6aa-250">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="1a6aa-250">Examine the project files</span></span>

<span data-ttu-id="1a6aa-251">Aşağıda, daha sonraki eğitimlerde çalışacağınız ana proje klasörleri ve dosyalarına genel bir bakış verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1a6aa-252">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="1a6aa-252">Pages folder</span></span>

<span data-ttu-id="1a6aa-253">Razor sayfaları ve destekleyici dosyalar içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1a6aa-254">Her Razor sayfası bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1a6aa-255">Razor sözdizimini kullanarak C# koduyla HTML biçimlendirmesi içeren *bir .cshtml* dosyası.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1a6aa-256">Sayfa olaylarını işleyen C# kodu içeren *.cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1a6aa-257">Destekleyen dosyaların alt altı yla başlayan adları vardır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1a6aa-258">Örneğin, *_Layout.cshtml* dosyası tüm sayfalarda yaygın olan UI öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1a6aa-259">Bu dosya, sayfanın üst kısmındaki gezinti menüsünü ve sayfanın altındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1a6aa-260">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1a6aa-261">wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="1a6aa-261">wwwroot folder</span></span>

<span data-ttu-id="1a6aa-262">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyalar içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1a6aa-263">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1a6aa-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1a6aa-264">appSettings.json</span></span>

<span data-ttu-id="1a6aa-265">Bağlantı dizeleri gibi yapılandırma verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1a6aa-266">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1a6aa-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1a6aa-267">Program.cs</span></span>

<span data-ttu-id="1a6aa-268">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-268">Contains the entry point for the program.</span></span> <span data-ttu-id="1a6aa-269">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1a6aa-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1a6aa-270">Startup.cs</span></span>

<span data-ttu-id="1a6aa-271">Çerezler için onay gerektiremesi gibi uygulama davranışını yapılandıran kod içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1a6aa-272">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1a6aa-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a6aa-273">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1a6aa-273">Additional resources</span></span>

* [<span data-ttu-id="1a6aa-274">Bu öğretici Youtube sürümü</span><span class="sxs-lookup"><span data-stu-id="1a6aa-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1a6aa-275">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1a6aa-275">Next steps</span></span>

<span data-ttu-id="1a6aa-276">Serinin bir sonraki öğretici için advance:</span><span class="sxs-lookup"><span data-stu-id="1a6aa-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1a6aa-277">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="1a6aa-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
