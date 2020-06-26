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
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405308"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="1ef39-104">Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="1ef39-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1ef39-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1ef39-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1ef39-106">Bu, bir serinin ASP.NET Core bir Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="1ef39-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1ef39-107">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1ef39-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="1ef39-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1ef39-109">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1ef39-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1ef39-110">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-110">Run the app.</span></span>
> * <span data-ttu-id="1ef39-111">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-111">Examine the project files.</span></span>

<span data-ttu-id="1ef39-112">Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1ef39-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1ef39-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ef39-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ef39-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ef39-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ef39-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1ef39-118">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="1ef39-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ef39-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1ef39-120">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1ef39-121">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1ef39-122">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1ef39-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1ef39-123">Projeyi **RazorPagesMovie**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1ef39-124">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *RazorPagesMovie* olarak adı vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1ef39-125">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1ef39-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1ef39-126">Açılan **Web uygulamasındaki** **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1ef39-128">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="1ef39-128">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ef39-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ef39-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1ef39-131">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1ef39-132">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1ef39-133">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1ef39-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1ef39-134">`dotnet new`Komut RazorPagesMovie klasöründe yeni bir Razor Sayfalar projesi oluşturur. *RazorPagesMovie*</span><span class="sxs-lookup"><span data-stu-id="1ef39-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1ef39-135">`code`Komut, Visual Studio Code geçerli örneğindeki *RazorPagesMovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="1ef39-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1ef39-136">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' RazorPagesMovie ' içinde eksik olduğunu soran bir iletişim kutusu yok. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="1ef39-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1ef39-137">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-137">Select **Yes**.</span></span>

  <span data-ttu-id="1ef39-138">Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ef39-139">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1ef39-140">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-140">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1ef39-142">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="1ef39-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="1ef39-143">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="1ef39-145">Aşağıdaki konfigürasyonları onaylayın:</span><span class="sxs-lookup"><span data-stu-id="1ef39-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="1ef39-146">**Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="1ef39-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="1ef39-147">**Kimlik doğrulaması** **yok**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="1ef39-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="1ef39-148">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-148">Select **Next**.</span></span>

  ![macOS .NET Core 3,1 seçimi](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1ef39-150">Projeyi **RazorPagesMovie**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1ef39-152">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="1ef39-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="1ef39-153">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="1ef39-153">Examine the project files</span></span>

<span data-ttu-id="1ef39-154">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1ef39-155">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="1ef39-155">Pages folder</span></span>

<span data-ttu-id="1ef39-156">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1ef39-157">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="1ef39-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1ef39-158">Sözdizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası. Razor</span><span class="sxs-lookup"><span data-stu-id="1ef39-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1ef39-159">Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="1ef39-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1ef39-160">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1ef39-161">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1ef39-162">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1ef39-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1ef39-163">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1ef39-164">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="1ef39-164">wwwroot folder</span></span>

<span data-ttu-id="1ef39-165">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1ef39-166">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1ef39-167">Üzerinde appSettings.js</span><span class="sxs-lookup"><span data-stu-id="1ef39-167">appSettings.json</span></span>

<span data-ttu-id="1ef39-168">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1ef39-169">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1ef39-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1ef39-170">Program.cs</span></span>

<span data-ttu-id="1ef39-171">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-171">Contains the entry point for the program.</span></span> <span data-ttu-id="1ef39-172">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1ef39-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1ef39-173">Startup.cs</span></span>

<span data-ttu-id="1ef39-174">Uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="1ef39-175">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1ef39-176">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1ef39-176">Next steps</span></span>

<span data-ttu-id="1ef39-177">Serideki bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="1ef39-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1ef39-178">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="1ef39-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1ef39-179">Bu, bir serinin ilk öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="1ef39-180">[Seriler](xref:tutorials/razor-pages/index) , ASP.NET Core sayfaları Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="1ef39-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1ef39-181">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1ef39-182">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="1ef39-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1ef39-183">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1ef39-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1ef39-184">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-184">Run the app.</span></span>
> * <span data-ttu-id="1ef39-185">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-185">Examine the project files.</span></span>

<span data-ttu-id="1ef39-186">Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1ef39-188">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1ef39-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ef39-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ef39-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ef39-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ef39-191">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1ef39-192">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="1ef39-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ef39-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1ef39-194">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1ef39-195">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1ef39-197">Projeyi **RazorPagesMovie**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1ef39-198">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *RazorPagesMovie* olarak adı vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* <span data-ttu-id="1ef39-200">Açılan **Web uygulamasındaki** **ASP.NET Core 2,2** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1ef39-202">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="1ef39-202">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ef39-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ef39-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1ef39-205">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1ef39-206">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1ef39-207">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1ef39-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1ef39-208">`dotnet new`Komut RazorPagesMovie klasöründe yeni bir Razor Sayfalar projesi oluşturur. *RazorPagesMovie*</span><span class="sxs-lookup"><span data-stu-id="1ef39-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1ef39-209">`code`Komut, Visual Studio Code geçerli örneğindeki *RazorPagesMovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="1ef39-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1ef39-210">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' RazorPagesMovie ' içinde eksik olduğunu soran bir iletişim kutusu yok. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="1ef39-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1ef39-211">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-211">Select **Yes**.</span></span>

  <span data-ttu-id="1ef39-212">Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ef39-213">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1ef39-214">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-214">Select **File** > **New Solution**.</span></span>

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1ef39-216">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="1ef39-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="1ef39-217">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="1ef39-218">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** Iletişim kutusunda **hedef Framework 'ü** **.NET Core 3,1**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,0 seçimi](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1ef39-220">Projeyi **RazorPagesMovie**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1ef39-222">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="1ef39-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ef39-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1ef39-224">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1ef39-225">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1ef39-226">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="1ef39-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1ef39-227">Bunun nedeni, `localhost` Yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1ef39-228">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1ef39-229">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1ef39-230">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1ef39-231">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1ef39-233">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1ef39-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="1ef39-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ef39-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1ef39-236">Hata ayıklayıcı olmadan çalıştırmak için **CTRL-F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1ef39-237">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="1ef39-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1ef39-238">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="1ef39-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1ef39-239">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1ef39-240">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1ef39-241">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1ef39-242">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1ef39-244">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1ef39-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ef39-246">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ef39-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1ef39-247">Hata ayıklayıcı olmadan çalıştırmak için **cmd-opt-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="1ef39-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1ef39-248">Visual Studio, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="1ef39-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1ef39-249">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1ef39-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1ef39-250">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1ef39-252">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1ef39-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1ef39-254">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="1ef39-254">Examine the project files</span></span>

<span data-ttu-id="1ef39-255">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1ef39-256">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="1ef39-256">Pages folder</span></span>

<span data-ttu-id="1ef39-257">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1ef39-258">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="1ef39-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1ef39-259">Sözdizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası. Razor</span><span class="sxs-lookup"><span data-stu-id="1ef39-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1ef39-260">Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="1ef39-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1ef39-261">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1ef39-262">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1ef39-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1ef39-263">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1ef39-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1ef39-264">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1ef39-265">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="1ef39-265">wwwroot folder</span></span>

<span data-ttu-id="1ef39-266">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1ef39-267">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1ef39-268">Üzerinde appSettings.js</span><span class="sxs-lookup"><span data-stu-id="1ef39-268">appSettings.json</span></span>

<span data-ttu-id="1ef39-269">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1ef39-270">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1ef39-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1ef39-271">Program.cs</span></span>

<span data-ttu-id="1ef39-272">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-272">Contains the entry point for the program.</span></span> <span data-ttu-id="1ef39-273">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1ef39-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1ef39-274">Startup.cs</span></span>

<span data-ttu-id="1ef39-275">Tanımlama bilgilerinin onayını gerektirip gerektirmediğini belirten uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="1ef39-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1ef39-276">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1ef39-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ef39-277">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1ef39-277">Additional resources</span></span>

* [<span data-ttu-id="1ef39-278">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="1ef39-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1ef39-279">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1ef39-279">Next steps</span></span>

<span data-ttu-id="1ef39-280">Serideki bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="1ef39-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1ef39-281">Model ekleme</span><span class="sxs-lookup"><span data-stu-id="1ef39-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
