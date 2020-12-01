---
title: 'Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama'
author: rick-anderson
description: Bu, bir serinin ASP.NET Core bir Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4b8bd9c886e615add6b0d3e372843a8ddb33ae18
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420051"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="3e68c-103">Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="3e68c-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="3e68c-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3e68c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="3e68c-105">Bu, bir serinin ASP.NET Core bir Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="3e68c-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="3e68c-106">Denetleyiciler ve görünümler hakkında bilgi sahibi olan geliştiricilere daha gelişmiş bir giriş için bkz. [ Razor sayfalara giriş](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="3e68c-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="3e68c-107">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="3e68c-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e68c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3e68c-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="3e68c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3e68c-110">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e68c-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3e68c-111">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-111">Run the app.</span></span>
> * <span data-ttu-id="3e68c-112">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-112">Examine the project files.</span></span>

<span data-ttu-id="3e68c-113">Bu öğreticinin sonunda, Razor sonraki öğreticilerde geliştireceğim bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="3e68c-115">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3e68c-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="3e68c-119">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="3e68c-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3e68c-121">Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="3e68c-122">Daha fazla bilgi için bkz. [Visual Studio 'da yeni proje oluşturma](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="3e68c-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Başlangıç penceresinden yeni bir proje oluştur](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="3e68c-124">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması**' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="3e68c-126">**Yeni projenizi yapılandırın** Iletişim kutusunda `RazorPagesMovie` **Proje adı**' nı girin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="3e68c-127">Büyük/küçük harf eşleştirme da dahil olmak üzere Project *Razor pagesmovie*'in adı, örnek kodu kopyalayıp yapıştırdığınızda ad alanlarının eşleşmesi önemlidir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="3e68c-128">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-128">Select **Create**.</span></span>

    ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

1. <span data-ttu-id="3e68c-130">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:</span><span class="sxs-lookup"><span data-stu-id="3e68c-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="3e68c-131">**.NET Core** ve **ASP.NET Core 5,0** açılır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="3e68c-132">**Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="3e68c-132">**Web Application**.</span></span>
    1. <span data-ttu-id="3e68c-133">**Create**.</span><span class="sxs-lookup"><span data-stu-id="3e68c-133">**Create**.</span></span>

     ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="3e68c-135">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3e68c-135">The following starter project is created:</span></span>

    ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3e68c-138">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="3e68c-139">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="3e68c-140">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3e68c-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="3e68c-141">`dotnet new`Komutu Razor *Razor pagesmovie* klasöründe yeni bir sayfalar projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e68c-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="3e68c-142">`code`Komutu, Visual Studio Code geçerli örneğindeki *Razor pagesmovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="3e68c-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-143">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3e68c-144">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-144">Select **File** > **New Solution**.</span></span>

    ![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="3e68c-146">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="3e68c-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="3e68c-147">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![macOS Web uygulaması şablon seçimi](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="3e68c-149">**Yeni Web uygulamasını Yapılandır** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="3e68c-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="3e68c-150">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="3e68c-151">**Hedef çerçeve** seçme seçeneği sunulursa, en son .NET 5. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="3e68c-152">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-152">Select **Next**.</span></span>

1. <span data-ttu-id="3e68c-153">Projeyi *Razor pagesmovie* olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS projeyi Adlandır](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3e68c-155">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="3e68c-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="3e68c-156">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="3e68c-156">Examine the project files</span></span>

<span data-ttu-id="3e68c-157">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3e68c-158">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="3e68c-158">Pages folder</span></span>

<span data-ttu-id="3e68c-159">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3e68c-160">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="3e68c-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3e68c-161">Sözdizimi kullanılarak C# kodu ile HTML biçimlendirmesine sahip bir *. cshtml* dosyası Razor .</span><span class="sxs-lookup"><span data-stu-id="3e68c-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3e68c-162">Sayfa olaylarını işleyen C# koduna sahip bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="3e68c-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="3e68c-163">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3e68c-164">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3e68c-165">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3e68c-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3e68c-166">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3e68c-167">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="3e68c-167">wwwroot folder</span></span>

<span data-ttu-id="3e68c-168">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3e68c-169">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="3e68c-170">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="3e68c-171">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3e68c-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="3e68c-172">Program.cs</span></span>

<span data-ttu-id="3e68c-173">Uygulamanın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-173">Contains the entry point for the app.</span></span> <span data-ttu-id="3e68c-174">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3e68c-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3e68c-175">Startup.cs</span></span>

<span data-ttu-id="3e68c-176">Uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="3e68c-177">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3e68c-178">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="3e68c-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3e68c-179">Sonraki: model ekleme</span><span class="sxs-lookup"><span data-stu-id="3e68c-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3e68c-180">Bu, bir serinin ASP.NET Core bir Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="3e68c-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="3e68c-181">Denetleyiciler ve görünümler hakkında bilgi sahibi olan geliştiricilere daha gelişmiş bir giriş için bkz. [ Razor sayfalara giriş](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="3e68c-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="3e68c-182">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="3e68c-183">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e68c-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3e68c-184">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="3e68c-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3e68c-185">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e68c-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3e68c-186">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-186">Run the app.</span></span>
> * <span data-ttu-id="3e68c-187">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-187">Examine the project files.</span></span>

<span data-ttu-id="3e68c-188">Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="3e68c-190">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3e68c-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-193">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="3e68c-194">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="3e68c-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3e68c-196">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3e68c-197">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="3e68c-198">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="3e68c-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="3e68c-199">Projeyi **Razor pagesfilmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3e68c-200">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *Razor pagesfilmi* olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="3e68c-201">![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="3e68c-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="3e68c-202">Açılan **Web uygulamasındaki** **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="3e68c-204">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3e68c-204">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3e68c-207">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3e68c-208">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="3e68c-209">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3e68c-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3e68c-210">`dotnet new`Komutu Razor *Razor pagesmovie* klasöründe yeni bir sayfalar projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e68c-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3e68c-211">`code`Komutu, Visual Studio Code geçerli örneğindeki *Razor pagesmovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="3e68c-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="3e68c-212">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' pagesmovie ' içinde eksik bir iletişim kutusu yok Razor . Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="3e68c-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="3e68c-213">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-213">Select **Yes**.</span></span>

  <span data-ttu-id="3e68c-214">Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-215">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3e68c-216">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-216">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3e68c-218">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="3e68c-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="3e68c-219">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="3e68c-221">**Yeni Web uygulamasını Yapılandır** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="3e68c-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="3e68c-222">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="3e68c-223">**Hedef çerçeve** seçme seçeneği sunulursa, en son 3. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="3e68c-224">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-224">Select **Next**.</span></span>

* <span data-ttu-id="3e68c-225">Projeyi **Razor pagesfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3e68c-227">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="3e68c-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="3e68c-228">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="3e68c-228">Examine the project files</span></span>

<span data-ttu-id="3e68c-229">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3e68c-230">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="3e68c-230">Pages folder</span></span>

<span data-ttu-id="3e68c-231">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3e68c-232">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="3e68c-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3e68c-233">Sözdizimi kullanılarak C# kodu ile HTML biçimlendirmesine sahip bir *. cshtml* dosyası Razor .</span><span class="sxs-lookup"><span data-stu-id="3e68c-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3e68c-234">Sayfa olaylarını işleyen C# koduna sahip bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="3e68c-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="3e68c-235">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3e68c-236">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3e68c-237">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3e68c-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3e68c-238">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3e68c-239">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="3e68c-239">wwwroot folder</span></span>

<span data-ttu-id="3e68c-240">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3e68c-241">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3e68c-242">Üzerinde appSettings.js</span><span class="sxs-lookup"><span data-stu-id="3e68c-242">appSettings.json</span></span>

<span data-ttu-id="3e68c-243">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="3e68c-244">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3e68c-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="3e68c-245">Program.cs</span></span>

<span data-ttu-id="3e68c-246">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-246">Contains the entry point for the program.</span></span> <span data-ttu-id="3e68c-247">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3e68c-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3e68c-248">Startup.cs</span></span>

<span data-ttu-id="3e68c-249">Uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="3e68c-250">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3e68c-251">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="3e68c-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3e68c-252">Sonraki: model ekleme</span><span class="sxs-lookup"><span data-stu-id="3e68c-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e68c-253">Bu, bir serinin ilk öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="3e68c-254">[Seriler](xref:tutorials/razor-pages/index) , ASP.NET Core sayfaları Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .</span><span class="sxs-lookup"><span data-stu-id="3e68c-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="3e68c-255">Denetleyiciler ve görünümler hakkında bilgi sahibi olan geliştiricilere daha gelişmiş bir giriş için bkz. [ Razor sayfalara giriş](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="3e68c-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="3e68c-256">Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="3e68c-257">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e68c-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3e68c-258">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="3e68c-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3e68c-259">Bir Razor Sayfalar Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e68c-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3e68c-260">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-260">Run the app.</span></span>
> * <span data-ttu-id="3e68c-261">Proje dosyalarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-261">Examine the project files.</span></span>

<span data-ttu-id="3e68c-262">Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="3e68c-264">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3e68c-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-267">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="3e68c-268">RazorSayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="3e68c-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3e68c-270">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="3e68c-271">Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="3e68c-273">Projeyi **Razor pagesfilmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3e68c-274">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *Razor pagesfilmi* olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* <span data-ttu-id="3e68c-276">Açılan **Web uygulamasındaki** **ASP.NET Core 2,2** ' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="3e68c-278">Aşağıdaki Başlatıcı proje oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3e68c-278">The following starter project is created:</span></span>

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3e68c-281">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3e68c-282">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="3e68c-283">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3e68c-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3e68c-284">`dotnet new`Komutu Razor *Razor pagesmovie* klasöründe yeni bir sayfalar projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e68c-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3e68c-285">`code`Komutu, Visual Studio Code geçerli örneğindeki *Razor pagesmovie* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="3e68c-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="3e68c-286">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' pagesmovie ' içinde eksik bir iletişim kutusu yok Razor . Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="3e68c-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="3e68c-287">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-287">Select **Yes**.</span></span>

  <span data-ttu-id="3e68c-288">Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-289">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3e68c-290">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-290">Select **File** > **New Solution**.</span></span>

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3e68c-292">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="3e68c-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="3e68c-293">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="3e68c-294">**Yeni Web uygulamasını Yapılandır** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="3e68c-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="3e68c-295">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="3e68c-296">**Hedef çerçeve** seçme seçeneği sunulursa, en son 2. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="3e68c-297">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-297">Select **Next**.</span></span>

* <span data-ttu-id="3e68c-298">Projeyi **Razor pagesfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3e68c-300">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="3e68c-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e68c-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3e68c-302">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="3e68c-303">Uygulamayı <kbd>CTRL + F5</kbd> (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3e68c-304">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="3e68c-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="3e68c-305">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3e68c-306">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="3e68c-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3e68c-307">Bunun nedeni, `localhost` Yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="3e68c-308">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="3e68c-309">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="3e68c-310">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3e68c-311">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3e68c-313">Aşağıdaki görüntüde, izleme onayı sağlandığında uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3e68c-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="3e68c-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e68c-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="3e68c-316">Hata ayıklayıcı olmadan çalıştırmak için <kbd>CTRL + F5</kbd> tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="3e68c-317">Uygulamayı <kbd>CTRL + F5</kbd> (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3e68c-318">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="3e68c-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="3e68c-319">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve ' a gider `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="3e68c-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="3e68c-320">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="3e68c-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3e68c-321">Bunun nedeni, `localhost` Yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="3e68c-322">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="3e68c-323">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3e68c-324">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3e68c-326">Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3e68c-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e68c-328">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e68c-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3e68c-329">Hata ayıklayıcı olmadan çalıştırmak için **cmd-opt-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="3e68c-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3e68c-330">Uygulamayı <kbd>cmd + opt + F5</kbd> (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3e68c-331">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="3e68c-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="3e68c-332">Visual Studio, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve ' a gider `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="3e68c-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="3e68c-333">Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e68c-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3e68c-334">Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="3e68c-336">Aşağıdaki görüntüde, izleme onayı sağlandığında uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3e68c-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home veya::: No-Loc (Dizin)::: sayfa](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="3e68c-338">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="3e68c-338">Examine the project files</span></span>

<span data-ttu-id="3e68c-339">Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3e68c-340">Sayfalar klasörü</span><span class="sxs-lookup"><span data-stu-id="3e68c-340">Pages folder</span></span>

<span data-ttu-id="3e68c-341">RazorSayfaları ve destekleyici dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3e68c-342">Her Razor sayfa bir dosya çiftidir:</span><span class="sxs-lookup"><span data-stu-id="3e68c-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3e68c-343">Sözdizimi kullanılarak C# kodu ile HTML biçimlendirmesine sahip bir *. cshtml* dosyası Razor .</span><span class="sxs-lookup"><span data-stu-id="3e68c-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3e68c-344">Sayfa olaylarını işleyen C# koduna sahip bir *. cshtml.cs* dosyası.</span><span class="sxs-lookup"><span data-stu-id="3e68c-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="3e68c-345">Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3e68c-346">Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3e68c-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3e68c-347">Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3e68c-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3e68c-348">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="3e68c-349">Razor Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="3e68c-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="3e68c-350">Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="3e68c-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3e68c-351">Wwwroot klasörü</span><span class="sxs-lookup"><span data-stu-id="3e68c-351">wwwroot folder</span></span>

<span data-ttu-id="3e68c-352">HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3e68c-353">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3e68c-354">Üzerinde appSettings.js</span><span class="sxs-lookup"><span data-stu-id="3e68c-354">appSettings.json</span></span>

<span data-ttu-id="3e68c-355">Bağlantı dizeleri gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="3e68c-356">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3e68c-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="3e68c-357">Program.cs</span></span>

<span data-ttu-id="3e68c-358">Programın giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="3e68c-358">Contains the entry point for the program.</span></span> <span data-ttu-id="3e68c-359">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3e68c-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3e68c-360">Startup.cs</span></span>

<span data-ttu-id="3e68c-361">Uygulama davranışını yapılandıran kodu içerir, örneğin, için izin gerektirip gerektirmediğini belirtir cookie .</span><span class="sxs-lookup"><span data-stu-id="3e68c-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="3e68c-362">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3e68c-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e68c-363">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e68c-363">Additional resources</span></span>

* [<span data-ttu-id="3e68c-364">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="3e68c-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="3e68c-365">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="3e68c-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3e68c-366">Sonraki: model ekleme</span><span class="sxs-lookup"><span data-stu-id="3e68c-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
