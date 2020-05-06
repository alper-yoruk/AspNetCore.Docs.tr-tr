---
title: 'Öğretici: ASP.NET Core Razor sayfaları kullanmaya başlama'
author: rick-anderson
description: Bu öğretici dizisinde ASP.NET Core sayfaların nasıl kullanılacağı Razor gösterilmektedir. Model oluşturmayı, sayfalar için Razor kod oluşturmayı, veri erişimi için Entity Framework Core ve SQL Server kullanmayı, arama işlevselliği eklemeyi, giriş doğrulaması eklemeyi ve geçiş yapmayı kullanarak modeli güncelleştirme hakkında bilgi edinin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774879"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="9065f-104">Öğretici: ASP.NET Core Razor Pages ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="9065f-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="9065f-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9065f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="9065f-106">Bu, bir serinin ASP.NET Core Razor Pages Web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="9065f-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="9065f-107">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="9065f-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="9065f-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="9065f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9065f-109">Razor Pages bir Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9065f-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="9065f-110">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9065f-110">Run the app.</span></span>
> * <span data-ttu-id="9065f-111">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9065f-111">Examine the project files.</span></span>

<span data-ttu-id="9065f-112">Bu öğreticinin sonunda, daha sonraki öğreticilerde oluşturacağınız çalışan bir Razor Pages Web uygulamasına sahipsiniz.</span><span class="sxs-lookup"><span data-stu-id="9065f-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="9065f-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="9065f-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9065f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9065f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9065f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9065f-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="9065f-118">Razor Sayfaları web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="9065f-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9065f-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9065f-120">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9065f-121">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="9065f-122">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="9065f-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="9065f-123">Projeyi **RazorPagesMovie**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="9065f-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="9065f-124">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *RazorPagesMovie* olarak adı vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="9065f-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="9065f-125">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="9065f-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="9065f-126">Açılan **Web uygulamasındaki** **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="9065f-128">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9065f-128">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9065f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9065f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9065f-131">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="9065f-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="9065f-132">Projeyi içerecek dizine (`cd`) geçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="9065f-133">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9065f-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="9065f-134">`dotnet new` Komut *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9065f-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="9065f-135">Komut, Visual Studio Code geçerli örneğindeki RazorPagesMovie klasörünü açar. *RazorPagesMovie* `code`</span><span class="sxs-lookup"><span data-stu-id="9065f-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="9065f-136">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' RazorPagesMovie ' içinde eksik olduğunu soran bir iletişim kutusu yok. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="9065f-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="9065f-137">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-137">Select **Yes**.</span></span>

  <span data-ttu-id="9065f-138">*Launch. JSON* ve *Tasks. JSON* dosyalarını içeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="9065f-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9065f-139">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9065f-140">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-140">Select **File** > **New Solution**.</span></span>

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="9065f-142">**Daha sonra** **.NET Core** > **App** > **Web uygulaması** > ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="9065f-144">**Yeni Web uygulamanızı yapılandırın** Iletişim kutusunda **hedef Framework 'ü** **.NET Core 3,1**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9065f-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,1 seçimi](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="9065f-146">Projeyi **RazorPagesMovie**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="9065f-148">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="9065f-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="9065f-149">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="9065f-149">Examine the project files</span></span>

<span data-ttu-id="9065f-150">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9065f-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="9065f-151">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="9065f-151">Pages folder</span></span>

<span data-ttu-id="9065f-152">Razor sayfaları ve destekleyici dosyalar içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="9065f-153">Her Razor sayfası bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="9065f-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="9065f-154">Razor söz dizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası.</span><span class="sxs-lookup"><span data-stu-id="9065f-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="9065f-155">Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="9065f-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="9065f-156">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9065f-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="9065f-157">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9065f-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="9065f-158">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9065f-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="9065f-159">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9065f-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="9065f-160">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="9065f-160">wwwroot folder</span></span>

<span data-ttu-id="9065f-161">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="9065f-162">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="9065f-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="9065f-163">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="9065f-163">appSettings.json</span></span>

<span data-ttu-id="9065f-164">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="9065f-165">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9065f-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="9065f-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="9065f-166">Program.cs</span></span>

<span data-ttu-id="9065f-167">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-167">Contains the entry point for the program.</span></span> <span data-ttu-id="9065f-168">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9065f-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="9065f-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9065f-169">Startup.cs</span></span>

<span data-ttu-id="9065f-170">Uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="9065f-171">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9065f-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9065f-172">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9065f-172">Next steps</span></span>

<span data-ttu-id="9065f-173">Serideki bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="9065f-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9065f-174">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="9065f-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9065f-175">Bu, bir serinin ilk öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="9065f-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="9065f-176">[Seriler](xref:tutorials/razor-pages/index) , bir ASP.NET Core Razor pages Web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="9065f-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="9065f-177">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="9065f-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="9065f-178">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="9065f-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9065f-179">Razor Pages bir Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9065f-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="9065f-180">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9065f-180">Run the app.</span></span>
> * <span data-ttu-id="9065f-181">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9065f-181">Examine the project files.</span></span>

<span data-ttu-id="9065f-182">Bu öğreticinin sonunda, daha sonraki öğreticilerde oluşturacağınız çalışan bir Razor Pages Web uygulamasına sahipsiniz.</span><span class="sxs-lookup"><span data-stu-id="9065f-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="9065f-184">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="9065f-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9065f-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9065f-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9065f-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9065f-187">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="9065f-188">Razor Sayfaları web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="9065f-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9065f-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9065f-190">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="9065f-191">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="9065f-193">Projeyi **RazorPagesMovie**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="9065f-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="9065f-194">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *RazorPagesMovie* olarak adı vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="9065f-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* <span data-ttu-id="9065f-196">Açılan **Web uygulamasındaki** **ASP.NET Core 2,2** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="9065f-198">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9065f-198">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9065f-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9065f-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9065f-201">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="9065f-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="9065f-202">Projeyi içerecek dizine (`cd`) geçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="9065f-203">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9065f-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="9065f-204">`dotnet new` Komut *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9065f-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="9065f-205">Komut, Visual Studio Code geçerli örneğindeki RazorPagesMovie klasörünü açar. *RazorPagesMovie* `code`</span><span class="sxs-lookup"><span data-stu-id="9065f-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="9065f-206">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' RazorPagesMovie ' içinde eksik olduğunu soran bir iletişim kutusu yok. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="9065f-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="9065f-207">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-207">Select **Yes**.</span></span>

  <span data-ttu-id="9065f-208">*Launch. JSON* ve *Tasks. JSON* dosyalarını içeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="9065f-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9065f-209">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9065f-210">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-210">Select **File** > **New Solution**.</span></span>

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="9065f-212">**Daha sonra** **.NET Core** > **App** > **Web uygulaması** > ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="9065f-214">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** Iletişim kutusunda **hedef Framework 'ü** **.NET Core 3,1**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9065f-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,0 seçimi](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="9065f-216">Projeyi **RazorPagesMovie**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="9065f-218">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="9065f-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9065f-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9065f-220">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="9065f-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9065f-221">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="9065f-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="9065f-222">Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="9065f-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9065f-223">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="9065f-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="9065f-224">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="9065f-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="9065f-225">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9065f-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="9065f-226">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9065f-227">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="9065f-229">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9065f-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="9065f-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9065f-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="9065f-232">Hata ayıklayıcı olmadan çalıştırmak için **CTRL-F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="9065f-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9065f-233">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9065f-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="9065f-234">Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="9065f-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9065f-235">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="9065f-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="9065f-236">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="9065f-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="9065f-237">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9065f-238">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="9065f-240">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9065f-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9065f-242">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9065f-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="9065f-243">Hata ayıklayıcı olmadan çalıştırmak için **cmd-opt-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="9065f-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9065f-244">Visual Studio, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9065f-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="9065f-245">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="9065f-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9065f-246">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="9065f-248">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9065f-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="9065f-250">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="9065f-250">Examine the project files</span></span>

<span data-ttu-id="9065f-251">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9065f-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="9065f-252">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="9065f-252">Pages folder</span></span>

<span data-ttu-id="9065f-253">Sayfaları Razor ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="9065f-254">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="9065f-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="9065f-255">Sözdizimi kullanarak Razor C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası.</span><span class="sxs-lookup"><span data-stu-id="9065f-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="9065f-256">Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="9065f-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="9065f-257">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9065f-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="9065f-258">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9065f-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="9065f-259">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9065f-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="9065f-260">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9065f-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="9065f-261">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="9065f-261">wwwroot folder</span></span>

<span data-ttu-id="9065f-262">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="9065f-263">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="9065f-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="9065f-264">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="9065f-264">appSettings.json</span></span>

<span data-ttu-id="9065f-265">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="9065f-266">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9065f-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="9065f-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="9065f-267">Program.cs</span></span>

<span data-ttu-id="9065f-268">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-268">Contains the entry point for the program.</span></span> <span data-ttu-id="9065f-269">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9065f-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="9065f-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9065f-270">Startup.cs</span></span>

<span data-ttu-id="9065f-271">Tanımlama bilgilerinin onayını gerektirip gerektirmediğini belirten uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="9065f-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="9065f-272">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9065f-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9065f-273">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9065f-273">Additional resources</span></span>

* [<span data-ttu-id="9065f-274">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="9065f-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="9065f-275">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9065f-275">Next steps</span></span>

<span data-ttu-id="9065f-276">Serideki bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="9065f-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9065f-277">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="9065f-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
