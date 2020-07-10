---
title: 'Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama'
author: rick-anderson
description: Bu öğretici dizisinde ASP.NET Core sayfaların nasıl kullanılacağı gösterilmektedir Razor . Model oluşturmayı, sayfalar için kod oluşturmayı, Razor veri erişimi için Entity Framework Core ve SQL Server kullanmayı, arama işlevselliği eklemeyi, giriş doğrulaması eklemeyi ve geçiş yapmayı kullanarak modeli güncelleştirme hakkında bilgi edinin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 77b230f14b4eef60d771daf8fe09288a9dd3c69c
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212990"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="a3907-104">Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="a3907-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a3907-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a3907-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="a3907-106">Bu, bir serinin ASP.NET Core bir Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="a3907-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a3907-107">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="a3907-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a3907-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="a3907-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a3907-109">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3907-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a3907-110">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a3907-110">Run the app.</span></span>
> * <span data-ttu-id="a3907-111">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="a3907-111">Examine the project files.</span></span>

<span data-ttu-id="a3907-112">Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="a3907-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a3907-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a3907-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a3907-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a3907-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3907-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a3907-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="a3907-118">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a3907-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a3907-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a3907-120">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a3907-121">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="a3907-122">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="a3907-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="a3907-123">Projeyi \*\* Razor pagesfilmi\*\*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a3907-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a3907-124">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project \* Razor pagesfilmi\* olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="a3907-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="a3907-125">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="a3907-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="a3907-126">Açılan **Web uygulamasındaki** **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="a3907-128">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="a3907-128">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a3907-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3907-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a3907-131">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="a3907-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a3907-132">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a3907-133">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a3907-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a3907-134">`dotnet new`Komutu Razor \* Razor pagesmovie\* klasöründe yeni bir sayfalar projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a3907-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a3907-135">`code`Komutu, Visual Studio Code geçerli örneğindeki \* Razor pagesmovie\* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="a3907-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a3907-136">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' pagesmovie ' içinde eksik bir iletişim kutusu yok Razor . Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="a3907-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a3907-137">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-137">Select **Yes**.</span></span>

  <span data-ttu-id="a3907-138">Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3907-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a3907-139">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a3907-140">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-140">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a3907-142">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="a3907-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a3907-143">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="a3907-145">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="a3907-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a3907-146">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a3907-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a3907-147">**Hedef çerçeve**seçme seçeneği sunulursa, en son 3. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="a3907-148">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-148">Select **Next**.</span></span>

* <span data-ttu-id="a3907-149">Projeyi \*\* Razor pagesfilmi\*\*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a3907-151">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a3907-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="a3907-152">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="a3907-152">Examine the project files</span></span>

<span data-ttu-id="a3907-153">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a3907-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a3907-154">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="a3907-154">Pages folder</span></span>

<span data-ttu-id="a3907-155">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a3907-156">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="a3907-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a3907-157">Sözdizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası. Razor</span><span class="sxs-lookup"><span data-stu-id="a3907-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a3907-158">Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="a3907-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a3907-159">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="a3907-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a3907-160">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a3907-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a3907-161">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a3907-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a3907-162">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a3907-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a3907-163">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="a3907-163">wwwroot folder</span></span>

<span data-ttu-id="a3907-164">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a3907-165">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a3907-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a3907-166">Üzerinde appSettings.js</span><span class="sxs-lookup"><span data-stu-id="a3907-166">appSettings.json</span></span>

<span data-ttu-id="a3907-167">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a3907-168">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a3907-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a3907-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="a3907-169">Program.cs</span></span>

<span data-ttu-id="a3907-170">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-170">Contains the entry point for the program.</span></span> <span data-ttu-id="a3907-171">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a3907-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a3907-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a3907-172">Startup.cs</span></span>

<span data-ttu-id="a3907-173">Uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="a3907-174">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a3907-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a3907-175">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="a3907-175">Next steps</span></span>

<span data-ttu-id="a3907-176">Serideki bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="a3907-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a3907-177">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="a3907-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a3907-178">Bu, bir serinin ilk öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="a3907-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="a3907-179">[Seriler](xref:tutorials/razor-pages/index) , ASP.NET Core sayfaları Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="a3907-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a3907-180">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="a3907-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a3907-181">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="a3907-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a3907-182">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3907-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a3907-183">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a3907-183">Run the app.</span></span>
> * <span data-ttu-id="a3907-184">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="a3907-184">Examine the project files.</span></span>

<span data-ttu-id="a3907-185">Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="a3907-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a3907-187">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a3907-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a3907-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a3907-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3907-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a3907-190">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="a3907-191">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a3907-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a3907-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a3907-193">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="a3907-194">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="a3907-196">Projeyi \*\* Razor pagesfilmi\*\*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a3907-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a3907-197">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project \* Razor pagesfilmi\* olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="a3907-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* <span data-ttu-id="a3907-199">Açılan **Web uygulamasındaki** **ASP.NET Core 2,2** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="a3907-201">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="a3907-201">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a3907-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3907-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a3907-204">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="a3907-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a3907-205">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a3907-206">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a3907-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a3907-207">`dotnet new`Komutu Razor \* Razor pagesmovie\* klasöründe yeni bir sayfalar projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a3907-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a3907-208">`code`Komutu, Visual Studio Code geçerli örneğindeki \* Razor pagesmovie\* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="a3907-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a3907-209">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' pagesmovie ' içinde eksik bir iletişim kutusu yok Razor . Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="a3907-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a3907-210">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-210">Select **Yes**.</span></span>

  <span data-ttu-id="a3907-211">Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3907-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a3907-212">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a3907-213">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-213">Select **File** > **New Solution**.</span></span>

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a3907-215">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="a3907-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a3907-216">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="a3907-217">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="a3907-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a3907-218">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a3907-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a3907-219">**Hedef çerçeve**seçme seçeneği sunulursa, en son 2. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="a3907-220">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-220">Select **Next**.</span></span>

* <span data-ttu-id="a3907-221">Projeyi \*\* Razor pagesfilmi\*\*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a3907-223">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a3907-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a3907-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a3907-225">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="a3907-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a3907-226">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="a3907-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a3907-227">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="a3907-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a3907-228">Bunun nedeni, `localhost` Yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="a3907-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a3907-229">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="a3907-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a3907-230">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a3907-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="a3907-231">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a3907-232">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a3907-234">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="a3907-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="a3907-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3907-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a3907-237">Hata ayıklayıcı olmadan çalıştırmak için **CTRL-F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="a3907-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a3907-238">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a3907-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a3907-239">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="a3907-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a3907-240">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="a3907-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a3907-241">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="a3907-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="a3907-242">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a3907-243">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a3907-245">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="a3907-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a3907-247">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3907-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a3907-248">Hata ayıklayıcı olmadan çalıştırmak için **cmd-opt-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a3907-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a3907-249">Visual Studio, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a3907-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="a3907-250">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a3907-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a3907-251">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="a3907-253">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="a3907-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="a3907-255">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="a3907-255">Examine the project files</span></span>

<span data-ttu-id="a3907-256">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a3907-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a3907-257">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="a3907-257">Pages folder</span></span>

<span data-ttu-id="a3907-258">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a3907-259">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="a3907-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a3907-260">Sözdizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası. Razor</span><span class="sxs-lookup"><span data-stu-id="a3907-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a3907-261">Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="a3907-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a3907-262">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="a3907-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a3907-263">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a3907-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a3907-264">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a3907-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a3907-265">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a3907-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a3907-266">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="a3907-266">wwwroot folder</span></span>

<span data-ttu-id="a3907-267">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a3907-268">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a3907-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a3907-269">Üzerinde appSettings.js</span><span class="sxs-lookup"><span data-stu-id="a3907-269">appSettings.json</span></span>

<span data-ttu-id="a3907-270">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a3907-271">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a3907-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a3907-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="a3907-272">Program.cs</span></span>

<span data-ttu-id="a3907-273">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-273">Contains the entry point for the program.</span></span> <span data-ttu-id="a3907-274">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a3907-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a3907-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a3907-275">Startup.cs</span></span>

<span data-ttu-id="a3907-276">Tanımlama bilgilerinin onayını gerektirip gerektirmediğini belirten uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="a3907-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="a3907-277">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a3907-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3907-278">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a3907-278">Additional resources</span></span>

* [<span data-ttu-id="a3907-279">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="a3907-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="a3907-280">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="a3907-280">Next steps</span></span>

<span data-ttu-id="a3907-281">Serideki bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="a3907-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a3907-282">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="a3907-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
