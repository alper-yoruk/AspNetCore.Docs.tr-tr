---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 01/20/2021
no-loc:
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710794"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="8b7ad-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="8b7ad-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="8b7ad-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b7ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8b7ad-105">Bu, denetleyiciler ve görünümlerle ASP.NET Core MVC web geliştirmesini öğretir bir serinin ilk öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="8b7ad-106">Serinin sonunda, film verilerini yöneten ve görüntüleyen bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="8b7ad-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b7ad-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-108">Create a web app.</span></span>
> * <span data-ttu-id="8b7ad-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8b7ad-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-110">Work with a database.</span></span>
> * <span data-ttu-id="8b7ad-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-111">Add search and validation.</span></span>

<span data-ttu-id="8b7ad-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8b7ad-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b7ad-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8b7ad-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b7ad-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b7ad-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b7ad-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b7ad-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="8b7ad-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="8b7ad-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b7ad-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b7ad-119">Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="8b7ad-120">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="8b7ad-121">**Yeni projenizi yapılandırın** Iletişim kutusunda `MvcMovie` **Proje adı**' nı girin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="8b7ad-122">*Mvcfilmi* adlı projeyi adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="8b7ad-123">Kod kopyalanırken, büyük küçük harf her `namespace` eşleştirmelere eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="8b7ad-124">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-124">Select **Create**.</span></span>
* <span data-ttu-id="8b7ad-125">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="8b7ad-126">**.NET Core** ve **ASP.NET Core 5,0** açılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="8b7ad-127">**ASP.NET Core Web uygulaması (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="8b7ad-128">**Create**.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-128">**Create**.</span></span>

![<span data-ttu-id="8b7ad-129">Yeni bir ASP.NET Core Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="8b7ad-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="8b7ad-130">Projeyi oluşturmaya yönelik alternatif yaklaşımlar için bkz. [Visual Studio 'da yeni proje oluşturma](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="8b7ad-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="8b7ad-131">Visual Studio, oluşturulan MVC projesi için varsayılan proje şablonunu kullandı.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="8b7ad-132">Oluşturulan proje:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-132">The created project:</span></span>

* <span data-ttu-id="8b7ad-133">, Çalışan bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-133">Is a working app.</span></span>
* <span data-ttu-id="8b7ad-134">Temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b7ad-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b7ad-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b7ad-136">Öğreticide VS Code anladığı varsayılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="8b7ad-137">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="8b7ad-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="8b7ad-138">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8b7ad-139">`cd`Projeyi içerecek dizine () geçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="8b7ad-140">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8b7ad-141">Gerekli varlıklar içeren bir iletişim kutusu görüntülenirse **ve hata ayıklama ' MvcMovie ' içinde yok. Bunlara eklensin mi?**, **Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="8b7ad-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="8b7ad-142">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8b7ad-143">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b7ad-144">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b7ad-145">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-145">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8b7ad-147">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="8b7ad-148">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="8b7ad-150">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="8b7ad-151">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="8b7ad-152">**Hedef çerçeve** seçme seçeneği sunulursa, en son 5. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="8b7ad-153">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-153">Select **Next**.</span></span>

* <span data-ttu-id="8b7ad-154">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="8b7ad-156">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="8b7ad-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b7ad-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b7ad-158">Uygulamayı hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="8b7ad-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-159">Visual Studio:</span></span>

  * <span data-ttu-id="8b7ad-160">[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)başlatır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="8b7ad-161">Uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-161">Runs the app.</span></span>

  <span data-ttu-id="8b7ad-162">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b7ad-163">Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="8b7ad-164">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="8b7ad-165">CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="8b7ad-166">Kod değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-166">Make code changes.</span></span>
* <span data-ttu-id="8b7ad-167">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-167">Save the file.</span></span>
* <span data-ttu-id="8b7ad-168">Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="8b7ad-169">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Hata ayıklama menüsü](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="8b7ad-171">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="8b7ad-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="8b7ad-173">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-173">The following image shows the app:</span></span>

![Giriş veya dizin sayfası](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b7ad-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b7ad-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8b7ad-176">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8b7ad-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="8b7ad-178">[Kestrel](xref:fundamentals/servers/kestrel) başlatır</span><span class="sxs-lookup"><span data-stu-id="8b7ad-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="8b7ad-179">Bir tarayıcı başlatır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-179">Launches a browser.</span></span>
  * <span data-ttu-id="8b7ad-180">Şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="8b7ad-181">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8b7ad-182">Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="8b7ad-183">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="8b7ad-184">CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="8b7ad-185">Kod değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-185">Make code changes.</span></span>
* <span data-ttu-id="8b7ad-186">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-186">Save the file.</span></span>
* <span data-ttu-id="8b7ad-187">Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b7ad-189">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b7ad-190"> > Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="8b7ad-191">Mac için Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="8b7ad-192">[Kestrel](xref:fundamentals/servers/index#kestrel) sunucusunu başlatır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="8b7ad-193">Bir tarayıcı başlatır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-193">Launches a browser.</span></span>
  * <span data-ttu-id="8b7ad-194">`http://localhost:port`, *Bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarası olduğunda şuraya gider.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="8b7ad-195">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b7ad-196">Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="8b7ad-197">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="8b7ad-198">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="8b7ad-199">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-199">The following image shows the app:</span></span>

![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8b7ad-201">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8b7ad-202">Sonraki: denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="8b7ad-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8b7ad-203">Bu, denetleyiciler ve görünümlerle ASP.NET Core MVC web geliştirmesini öğretir bir serinin ilk öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="8b7ad-204">Serinin sonunda, film verilerini yöneten ve görüntüleyen bir uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="8b7ad-205">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b7ad-206">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-206">Create a web app.</span></span>
> * <span data-ttu-id="8b7ad-207">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8b7ad-208">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-208">Work with a database.</span></span>
> * <span data-ttu-id="8b7ad-209">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-209">Add search and validation.</span></span>

<span data-ttu-id="8b7ad-210">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8b7ad-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b7ad-211">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8b7ad-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b7ad-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b7ad-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b7ad-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b7ad-214">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="8b7ad-215">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="8b7ad-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b7ad-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b7ad-217">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="8b7ad-218">**ASP.NET Core Web uygulaması** > **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Yeni bir ASP.NET Core Web uygulaması projesi oluşturma](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8b7ad-220">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="8b7ad-221">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni projenizi yapılandırın](start-mvc/_static/config.png)

* <span data-ttu-id="8b7ad-223">**Web uygulaması (Model-View-Controller)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="8b7ad-224">Açılan kutularda **.NET Core** ve **ASP.NET Core 3,1**' i seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="8b7ad-225">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="8b7ad-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="8b7ad-226">Visual Studio, oluşturulan MVC projesi için varsayılan proje şablonunu kullandı.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="8b7ad-227">Oluşturulan proje:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-227">The created project:</span></span>

* <span data-ttu-id="8b7ad-228">, Çalışan bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-228">Is a working app.</span></span>
* <span data-ttu-id="8b7ad-229">Temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b7ad-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b7ad-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b7ad-231">Öğreticide VS Code anladığı varsayılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="8b7ad-232">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="8b7ad-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="8b7ad-233">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8b7ad-234">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="8b7ad-235">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8b7ad-236">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunlara eklensin mi?**, **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="8b7ad-237">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8b7ad-238">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b7ad-239">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b7ad-240">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-240">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8b7ad-242">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="8b7ad-243">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="8b7ad-245">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="8b7ad-246">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="8b7ad-247">**Hedef çerçeve** seçme seçeneği sunulursa, en son 3. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="8b7ad-248">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-248">Select **Next**.</span></span>

* <span data-ttu-id="8b7ad-249">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="8b7ad-251">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="8b7ad-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b7ad-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b7ad-253">Uygulamayı hata ayıklamadan çalıştırmak için CTRL + F5 ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="8b7ad-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-254">Visual Studio:</span></span>

  * <span data-ttu-id="8b7ad-255">[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)başlatır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="8b7ad-256">Uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-256">Runs the app.</span></span>

  <span data-ttu-id="8b7ad-257">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b7ad-258">Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="8b7ad-259">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="8b7ad-260">CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="8b7ad-261">Kod değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-261">Make code changes.</span></span>
* <span data-ttu-id="8b7ad-262">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-262">Save the file.</span></span>
* <span data-ttu-id="8b7ad-263">Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="8b7ad-264">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

<span data-ttu-id="8b7ad-266">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="8b7ad-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="8b7ad-268">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-268">The following image shows the app:</span></span>

![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b7ad-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b7ad-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8b7ad-271">Uygulamayı hata ayıklamadan çalıştırmak için CTRL + F5 ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8b7ad-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="8b7ad-273">[Kestrel](xref:fundamentals/servers/kestrel) başlatır</span><span class="sxs-lookup"><span data-stu-id="8b7ad-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="8b7ad-274">Bir tarayıcı başlatır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-274">Launches a browser.</span></span>
  * <span data-ttu-id="8b7ad-275">Şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="8b7ad-276">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8b7ad-277">Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="8b7ad-278">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="8b7ad-279">CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="8b7ad-280">Kod değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-280">Make code changes.</span></span>
* <span data-ttu-id="8b7ad-281">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-281">Save the file.</span></span>
* <span data-ttu-id="8b7ad-282">Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b7ad-284">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b7ad-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b7ad-285"> > Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="8b7ad-286">Mac için Visual Studio: [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusunu başlatır, bir tarayıcı başlatır ve ' a gider; `http://localhost:port` burada *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="8b7ad-287">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b7ad-288">Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` .</span><span class="sxs-lookup"><span data-stu-id="8b7ad-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="8b7ad-289">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8b7ad-290">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="8b7ad-291">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="8b7ad-292">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8b7ad-292">The following image shows the app:</span></span>

![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8b7ad-294">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b7ad-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8b7ad-295">Sonraki</span><span class="sxs-lookup"><span data-stu-id="8b7ad-295">Next</span></span>](adding-controller.md)

::: moniker-end
